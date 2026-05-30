# SOLUTION — Project 03: Performance Optimization Initiative

> Read this *after* you have read the project README, requirements,
> architecture, and rubric, and after you have at least sketched a
> target-selection memo of your own. This file is not a worked
> implementation — there is no single "right" stack to optimize. It
> is the principal-engineer playbook for *how to think about* this
> capstone, written so the rubric (and the reality behind the
> rubric) makes sense.

## 1. Solution overview

A principal-grade performance project is not "I made the kernel
faster." It is a structured, multi-layer optimization campaign
whose **headline number is defensible**, whose **process is
reproducible**, and whose **win survives the next model release**.
The rubric in `projects/project-03-performance-optimization/rubric.md`
weights this explicitly: 25% of the score is methodology, 15% is
durability, 15% is campaign rigor, and only the *remaining* points
are about the engineering itself.

The shape of a passing (≥70/100) solution:

1. **A single named metric.** Pick one — `$/1M tokens`, `p99 TTFT
   at QPS=N on prompt-length-L`, `tokens/sec/GPU at SLO`, `MFU on
   batch-size-B with N H100s` — and defend the choice with a
   business reason. Do not pick three and hand-wave the trade-off.
2. **Pre-registered methodology.** Workload pinned (model rev,
   tokenizer rev, request distribution, hardware SKU, driver/CUDA
   pinned, NCCL pinned, container digest pinned), control vs.
   treatment defined, noise floor measured, sample size and
   minimum-detectable-effect computed *before* you start. Pre-
   registered = committed to git before the experiment runs.
3. **A campaign of ≥20 experiments** spanning the four layers —
   kernel, framework, system, policy — with hypothesis, change,
   before/after with 95% CI, attribution to the headline, and a
   decision (keep / kill / parking-lot). At least 5 negatives kept.
4. **Cross-layer narrative.** At least one symptom-to-root-cause
   story that crosses all four layers. A senior IC reading the
   design doc should be able to point to a tail-latency spike in
   a Grafana dashboard, follow it through a tracing screenshot
   into a profiler trace, and land at the kernel fix and the
   policy change that protect each other.
5. **Regression-prevention infrastructure.** A nightly perf job
   with statistical detection (Welch's t-test or a Bayesian
   equivalent), and a demonstrated catch of a synthetic ≥5%
   regression on a representative smoke test.
6. **An executive narrative.** A 1-page summary a VP can quote
   correctly in 90 seconds, and a 25–40 minute tech talk that
   tells a story (problem → measurement → top 3 wins →
   durability → next), not a tool tour.
7. **Operational safety.** Every production-touching change is
   feature-flagged, has a documented rollback runbook, and was
   evaluated against the reliability SLO, not only the perf SLO.

The deliverable in this solutions repo is **not** the optimized
system itself; the deliverable is the *judgment chain* that gets
the candidate to that system. That is what the worked example
below illustrates.

## 2. Worked answer or implementation

> The worked example below is a **template applied to a
> representative inference workload** (vLLM serving a 70B-class
> dense LLM on H100s with INT8/FP8). It is one of many valid
> shapes. A candidate optimizing FSDP training of the same model
> would arrive at a structurally similar campaign but with
> different bets. The point is the shape of the reasoning, not
> the specific knobs.

### 2.1 Target selection: why this metric

**Chosen metric:** `$/1M output tokens at p99 TTFT ≤ 250 ms,
QPS = 64, prompt-length distribution = production-mirror`.

**Why this and not the alternatives:**

| Candidate metric | Why rejected (here) |
|---|---|
| Raw `tokens/sec/GPU` | Decouples from SLO; you can "win" by accepting unbounded queue depth. |
| `p99 TTFT` alone | Decouples from cost; you can "win" by over-provisioning. |
| `MFU` | Useful for training, weak proxy for inference $/token; a high-MFU server with bad batching loses anyway. |
| Multiple metrics combined | Cannot be optimized as a single objective — pick one as the headline, **guard** the others. |

**Why the business cares:** at 70B-class scale, inference cost
dominates training cost on a 12-month horizon for any product
with non-trivial daily token volume; a 30% reduction in
`$/1M tokens` at unchanged SLO is a credibly board-reportable
margin shift.

The candidate's `docs/target-selection.md` should make this case
in ≤1 page, naming the dollar volume and the SLO at which the
business will or will not accept the win.

### 2.2 Methodology that survives review

The methodology a candidate writes must answer, in writing,
**before any optimization runs**:

1. **What is the workload?** Pinned model checkpoint, pinned
   tokenizer, fixed request corpus with a documented prompt-
   length and output-length distribution, fixed sampling
   parameters (temperature, top-p, max-tokens), fixed system
   prompts, fixed concurrency model. The corpus should mirror
   production within reason; if it cannot, the divergence is
   documented.
2. **What is the hardware?** GPU SKU + count, NVLink/NVSwitch
   topology, host CPU + NUMA topology, network fabric
   (NVLink / IB / RoCE), firmware versions, driver, CUDA
   toolkit. Pinned in the README. Drift here invalidates
   comparisons.
3. **What is the software stack?** Pinned vLLM (or TGI /
   TensorRT-LLM) version, pinned NCCL version, pinned PyTorch
   version, pinned Transformer Engine version, pinned
   container digest.
4. **What is the noise floor?** Run the baseline workload N
   times with no changes; report mean, 95% CI, coefficient of
   variation. A treatment effect smaller than ~3× the noise
   floor is below the detection threshold. State the minimum
   detectable effect (MDE) in absolute units.
5. **What is the experimental design?** Control vs. treatment;
   randomized order of runs within a batch (to absorb
   thermal / power drift); a fresh server start between runs
   (no warm-cache contamination); the same load generator
   replayed deterministically.
6. **What is the statistical test?** Welch's t-test on per-run
   summary statistics (because variance per run is unequal),
   p < 0.01 to claim significance, 95% bootstrap CI on the
   point estimate. State your N and your power before running.
7. **What is "win"?** A treatment is a *win* iff (a) the
   per-run mean is statistically distinguishable from baseline
   at p < 0.01, (b) the lower bound of the 95% CI clears the
   minimum-effect threshold, and (c) the reliability SLO
   (error rate, correctness diff, OOMs) is not violated.

Two practitioner pointers to study, not to copy: NVIDIA's
[GenAI-Perf](https://github.com/triton-inference-server/perf_analyzer/blob/main/genai-perf/README.md)
methodology and vLLM's
[benchmarks/README.md](https://github.com/vllm-project/vllm/tree/main/benchmarks)
both encode much of the above. They are the closest
"official" thing this space has.

### 2.3 The campaign — by layer

The four-layer split below is the rubric's spine. A campaign
that touches only kernels and framework caps at Level 2 on
Dimension 2 (20 pts). The discipline is to allocate budget
across all four.

#### Layer A — Kernel-level bets

The candidate picks one to three kernel-level bets, sized by
profile evidence, not enthusiasm. Plausible bets at the time
of writing:

- **FlashAttention-2 / FlashAttention-3** for attention
  ([Dao 2023](https://arxiv.org/abs/2307.08691),
  [Shah et al. 2024](https://arxiv.org/abs/2407.08608)). Wins
  on long sequences and on H100/H200 where FA3's TMA + WGMMA
  use is leveraged.
- **FP8 weights + activations** via NVIDIA's
  [Transformer Engine](https://docs.nvidia.com/deeplearning/transformer-engine/user-guide/index.html).
  Wins on H100/H200 where FP8 is hardware-accelerated; not on
  A100.
- **Weight-only quantization** for inference where the bottleneck
  is memory bandwidth on the decode path:
  [AWQ](https://arxiv.org/abs/2306.00978),
  [GPTQ](https://arxiv.org/abs/2210.17323),
  [SmoothQuant](https://arxiv.org/abs/2211.10438). Each has
  documented accuracy / latency trade-offs.
- **Custom Triton kernel** for an op the profile shows is
  non-trivially under-utilizing tensor cores. The
  [Triton tutorials](https://triton-lang.org/main/getting-started/tutorials/index.html)
  are the entry point; the kernel must come with a numerics
  test (output vs. PyTorch reference within tolerance) and a
  micro-benchmark.

For each: hypothesis (what does the profile say the win is
worth?), implementation, before/after with CI, layer
attribution (e.g., "attention kernel is now 38% of step time,
down from 61%"), and a Nsight Compute screenshot annotated to
show the change in tensor-core utilization or HBM bandwidth.

#### Layer B — Framework-level bets

For inference, the framework-level bets the candidate ranks
against profile evidence include:

- **Continuous batching** ([Yu et al., Orca, OSDI 2022](https://www.usenix.org/conference/osdi22/presentation/yu),
  realized in vLLM and TGI). Almost always a win over static
  batching for mixed-length workloads.
- **Paged KV cache** ([Kwon et al., vLLM/PagedAttention,
  SOSP 2023](https://dl.acm.org/doi/10.1145/3600006.3613165)).
  Wins by raising achievable batch size at fixed memory.
- **Chunked prefill** and **prefix caching** (see
  [vLLM features](https://docs.vllm.ai/en/latest/features/)).
  Wins on workloads with shared system prompts or long
  prefills.
- **Speculative decoding**
  ([Leviathan et al. 2023](https://arxiv.org/abs/2211.17192),
  [Chen et al. 2023](https://arxiv.org/abs/2302.01318);
  [Medusa](https://arxiv.org/abs/2401.10774),
  [EAGLE](https://arxiv.org/abs/2401.15077)). Wins on
  decode-bound workloads when the draft model is well-matched
  to the target.

For training, the framework-level bets reshape but the discipline
is the same: FSDP comms overlap (`use_orig_params=True`,
sharding strategy choice), ZeRO-3 vs. FSDP picking, selective
activation checkpointing on attention only, `torch.compile` with
guarded fallback, microbatch sizing against the comms/compute
crossover.

For each bet, the candidate writes an ADR: the bet, the
profile evidence motivating it, the alternatives considered,
the expected magnitude, the failure mode if the bet doesn't
hold, and the rollback.

#### Layer C — System-level bets

This is the layer where candidates most commonly underspend.
The rubric requires at least one measurable system-level win.
Plausible bets:

- **NCCL tuning** — `NCCL_ALGO`, `NCCL_PROTO`,
  `NCCL_NTHREADS`, `NCCL_MIN_NCHANNELS`, `NCCL_BUFFSIZE`.
  Reference: [NCCL docs](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html).
  Wins are workload-dependent; the campaign should sweep the
  short list against the actual communication pattern, not
  pick from a blog post.
- **NVLink / IB topology awareness** — pin ranks to GPUs so
  that ring or tree algorithms traverse fast links; use
  `nvidia-smi topo -m` and `dcgm-exporter` to verify.
- **NUMA pinning** — pin worker processes to the NUMA node
  closest to the GPU; pin the network IRQ; verify with
  `numastat` and `perf`. Wins are biggest for CPU-side
  tokenization / pre-processing.
- **GPU clock / power policy** — fix the clock to a known
  state for measurement; document whether production runs in
  the same state.
- **Hugepages**, container CPU pinning, IRQ affinity — only
  if the profile points there.

#### Layer D — Policy-level bets

The "above the framework" layer: how requests reach the
servers in the first place.

- **Request routing by sequence-length bucket** — prompts
  with similar prefill cost batched together to avoid head-
  of-line blocking on continuous-batching servers.
- **Autoscaling with predictive headroom** — scale on a
  forward-looking signal (e.g., concurrency × est-decode-
  length), not on instantaneous QPS, to avoid the SLO-violating
  spike between scale-out triggers.
- **Batch admission policy** — drop or shed low-priority
  requests under load to preserve SLO for the rest. See
  [SLO-aware shedding patterns](https://research.google/pubs/load-shedding-in-bigtable/)
  as background.
- **Cache-aware routing** — route requests with a known
  shared prefix to the replica that already has it warm in
  paged-KV; biggest wins for chatty multi-turn workloads.

### 2.4 Cross-layer narrative (the spine of the talk)

The candidate writes one symptom-to-root-cause story that
crosses all four layers. The shape:

> 1. **Symptom (policy):** p99 TTFT spikes from 180 ms to
>    900 ms at QPS=64 in 0.5% of windows, despite mean
>    TTFT being healthy.
> 2. **Layer-up trace (framework):** Continuous-batching
>    server's scheduler is interleaving a long prefill into
>    a batch of short decodes, blocking the decode iteration
>    for ~700 ms.
> 3. **Layer-down trace (kernel):** Prefill kernel is
>    HBM-bandwidth-bound on long sequences; FlashAttention-3
>    not enabled because checkpoint dtype was bf16 and FA3
>    code path required additional flags.
> 4. **Root cause (system):** Driver / CUDA combination was
>    one minor version behind the FA3 minimum; topology was
>    fine.
> 5. **Fix:** Enable chunked prefill (policy / framework),
>    upgrade driver to FA3-compatible (system), enable FA3
>    (kernel). Verify with replay of the same 0.5% window.

This is the section that a senior IC will read first. It is
where the project's *taste* is most visible: a candidate who
only wrote four parallel sections by layer hasn't done the
cross-layer work yet.

### 2.5 Regression prevention

Without this, the win is rented, not owned.

- A nightly perf CI job running the same fixed workload as
  the methodology defines, on a fixed test fleet, with
  fixed software.
- Statistical regression detection: Welch's t-test of the
  last N runs vs. the rolling baseline window; alert on
  p < 0.01 and a per-metric absolute threshold (so a
  significant-but-tiny drift doesn't page).
- A long-term drift chart per metric, retained for at
  least 90 days.
- A documented "perf regressed" runbook (`docs/perf-runbook.md`):
  how to bisect, how to roll back the offending change, how
  to escalate, who owns it.
- A **regression demo PR**: synthetically slow a path by
  ≥5%, push, watch the CI catch it, link the PR in
  `docs/regression-demo.md`. This is the artifact that
  proves the CI works; without it, the dimension caps at
  Level 2.

### 2.6 Executive narrative

The 1-page exec summary and the 25–40 minute talk are
graded together. The exec summary is for someone who will
quote it in a different room. The shape that works:

> **What we did:** We cut `$/1M output tokens` by N%
> at unchanged p99 TTFT for the production LLM serving
> tier, on the same hardware.
>
> **Why it matters:** At our current daily token volume of
> X, that is approximately $Y / month at unchanged user
> experience.
>
> **How we know:** Pre-registered methodology; ≥20 controlled
> experiments; nightly perf CI now in place; full rollback
> runbooks for every change that touched production.
>
> **What we did not do, and why:** [Two specific items the
> candidate considered and turned down, with the reason —
> proves taste, not just throughput.]
>
> **What's next:** [Concrete 90-day follow-ups, with named
> owners and the trigger conditions for revisiting.]

The talk arc is: **problem → measurement → top 3 wins →
durability → next**, with one slide on a failed
optimization and what killed it. A candidate whose talk
opens with a tool tour ("here's what we tried in vLLM") has
already lost the room.

### 2.7 Rollback, canary, reliability SLO

The rubric's lowest-weighted dimension (10 pts) is also the
fastest way to lose pass/fail status: a perf project that
**broke reliability** is not Level 3 regardless of the
headline. The deliverables:

- A feature flag per production-touching change.
- A canary plan with stages (typical: 1% → 10% → 50% →
  100%), each gated on a *breaking* metric (not only the
  one being optimized); the gate definitions are written
  down before canary starts.
- A reliability SLO check: error rate, OOM rate, output
  correctness diff (e.g., teacher-forced exact-match or
  judged-equivalence on a held-out eval set) before and
  after; the correctness diff is within a tolerance the
  candidate names.
- A rollback runbook per change: the specific commands or
  console clicks, the verification step, who is paged if
  the rollback itself misbehaves.

## 3. Validation steps

The candidate's own self-validation, before submitting:

1. **Reproducibility cold-start.** Pull the repo to a clean
   machine. Run `make baseline`. Numbers within stated CI?
   If not, the workload is not actually pinned.
2. **Random-experiment audit.** Pick three experiment IDs at
   random from `experiments/experiment-log.md`. For each: open
   the `repro/<id>/` directory, run it, check the result is
   within CI of what's in the log. If any drifts, the campaign
   has hidden dependencies.
3. **Methodology pre-registration check.** `git log` the
   `docs/methodology.md` file. Its commit timestamp must
   precede the first experiment commit timestamp. If not, the
   methodology was retro-fit and the headline is suspect.
4. **Profile evidence check.** Open every screenshot in
   `profiles/` and check there is a written sentence linking
   the screenshot to a specific experiment-log entry. Lonely
   screenshots ≠ evidence.
5. **Regression CI green-cycle check.** Confirm seven nightly
   runs in a row passed (or each failure is documented).
6. **Regression CI catches-regression check.** Open the PR
   linked in `docs/regression-demo.md`. Confirm it added a
   synthetic ≥5% slowdown, that the perf CI failed on it,
   and that the PR was reverted (not silently merged).
7. **Durability check.** Open `docs/durability.md`. For every
   optimization, is there a named follow-up owner and a named
   trigger (e.g., "vLLM ≥0.7.0 release", "model v3 ship",
   "H200 rollout")? Generic "we'll re-evaluate quarterly"
   answers do not count.
8. **Rollback dry-run.** Pick one rollback runbook at random;
   execute it on a non-production replica. Time it. Did the
   steps as written actually work?
9. **Reliability SLO check.** Confirm `docs/reliability-slo.md`
   reports pre/post numbers for at least: error rate, OOM
   rate, output correctness diff. Confirm none regressed.
10. **Exec summary 90-second read.** Have a non-engineer read
    it aloud and ask them to summarize the headline number,
    the business win, and what's next. If any of the three
    are wrong, rewrite the summary, not the talk.
11. **Talk dry-run with a hostile audience.** A perf-skeptical
    staff engineer is the right reviewer. They will ask:
    "how do I know this didn't just shift the bottleneck?",
    "what happens at QPS=2× this?", "what survives the next
    model release?" The candidate should have written
    answers, not improvised ones.

## 4. Rubric or review checklist

The full rubric is in
`projects/project-03-performance-optimization/rubric.md`. For
the reviewer, the principal-engineer-level signals to look for
(beyond the dimension-by-dimension scoring):

- [ ] The metric is **one named metric**, not "made faster".
      `docs/target-selection.md` defends the choice in business
      terms.
- [ ] The methodology was **pre-registered** (git timestamps
      verify) and would survive a peer at NVIDIA or Meta.
- [ ] The candidate **picked an order** for the optimizations
      and can defend it from profile evidence, not from
      enthusiasm.
- [ ] Wins are distributed across **all four layers** (kernel,
      framework, system, policy), with each layer contributing
      ≥10% of the headline at Level 4.
- [ ] At least one **cross-layer narrative** is written and is
      the spine of the tech talk.
- [ ] ≥20 experiments, ≥5 of them **negative**. Negatives are
      kept, not deleted; attribution discusses interactions
      between optimizations.
- [ ] **Perf CI** runs nightly with statistical detection; a
      regression-demo PR proves it catches ≥5%.
- [ ] **Durability section** names specific upcoming triggers
      (model release, framework upgrade, hardware refresh) and
      a named owner per follow-up.
- [ ] **Rollback runbook** exists for every production-touching
      change; one has been dry-run.
- [ ] **Reliability SLO** evaluated pre/post; correctness diff
      within a named tolerance.
- [ ] **Executive summary** is ≤1 page, non-engineer-readable
      in 90 s, names a credible $-or-user-impact framing
      (without inventing numbers).
- [ ] **Tech talk** tells a story (problem → measurement →
      top 3 wins → durability → next); has one failed-
      optimization slide; has one paragraph for each of: VP,
      model team, FinOps.
- [ ] The candidate can answer **"what did you turn down, and
      why?"** with specific examples. This is the single best
      proxy for principal-level taste in this rubric.
- [ ] At least three reviewers are named in the design doc
      front-matter (one perf specialist, one model owner, one
      FinOps / product partner).

Failure modes that drop the score by a level even when the
headline looks good:

- "Bigger than 2×" with single-run measurement → Level 2 on
  Dimension 1, not Level 4.
- A perf win that quietly broke a correctness check → not
  Level 3 on Dimension 6, no matter the score elsewhere.
- A talk that opens "let me show you what we tried in vLLM"
  → Level 2 on Dimension 5, regardless of the engineering.

## 5. Common mistakes

The grader's experience: most failed (or merely-passing)
submissions miss in one of the following ways.

1. **Optimizing before measuring.** The first profile is run
   *after* a kernel change. The win cannot be attributed.
   Symptom: the experiment log has experiment 001 as a code
   change, not as a baseline measurement.

2. **Single-run benchmarks.** Treat one number as truth; never
   compute variance; never report CI. A "30% win" that is 1.5σ
   inside noise is not a win. Pre-registering the methodology
   (with N and MDE) is the cheapest fix.

3. **Workload drift between baseline and treatment.** Different
   prompt corpus, different concurrency, different driver,
   different container — comparison is invalid. Pin everything,
   in a Makefile, in version control.

4. **Methodology written after the fact.** The reviewer checks
   commit timestamps. If `docs/methodology.md` was committed
   the night before submission, no number is defensible.

5. **Optimizing inside the wrong layer.** Months on a kernel
   that the profile says is 4% of step time; weeks on FSDP
   comms when the bottleneck is the data loader. The discipline
   is to profile first, write the layer budget in the design
   doc, then execute against it.

6. **Single-layer campaign.** All wins on kernel; nothing on
   framework / system / policy. Caps at Level 2 on Dimension
   2. The cure: explicitly allocate at least one bet per
   layer in the campaign plan.

7. **Throwing away negatives.** Only "wins" appear in the
   experiment log. A reviewer cannot tell whether the
   candidate considered the obvious-but-wrong alternatives.
   Negatives **add** rubric points; keep them.

8. **No cross-layer story.** Four parallel layer sections, no
   stitched narrative. The candidate did the work but did not
   teach a senior IC anything.

9. **No regression CI, or CI that doesn't catch anything.**
   Without the regression-demo PR, the dimension caps at
   Level 2 even if the CI exists.

10. **No durability discussion.** The reviewer asks "what
    survives the next model release?" and the answer is "all
    of it, I think." That is a wish, not an assessment.

11. **No rollback runbook.** The change is real, the runbook
    is "revert the commit." That works for one of N changes;
    the others have config, flags, dependencies. Each needs
    its own runbook.

12. **Reliability ignored.** Perf SLO defended; correctness /
    error rate / OOM not measured. A latency win that lifted
    OOM rate from 0.01% to 0.4% is a regression overall.

13. **Tech talk as feature tour.** "We tried FA3, then vLLM
    paged-KV, then NCCL, then …" — chronological, no
    narrative. The audience leaves remembering nothing.

14. **Exec summary too technical.** A non-engineer cannot
    quote the headline number correctly after reading. The
    summary is for a *different* audience than the talk; it
    needs its own draft.

15. **"What did you turn down?" answered with hand-waves.**
    A principal engineer is paid as much for saying no as for
    saying yes. A campaign that turned down nothing is a
    campaign that didn't take its budget seriously.

16. **Inventing a metric or a customer number.** The rubric
    will not reward a "$X saved" claim that has no audit
    trail. If real production numbers are not available, the
    candidate states this and uses unit framing (`$/1M
    tokens`) without dollarizing.

## 6. References

Official standards, framework documentation, and seminal
papers — the source set this solution is written against.

### Profiling and methodology

- NVIDIA Nsight Systems —
  <https://docs.nvidia.com/nsight-systems/UserGuide/index.html>
- NVIDIA Nsight Compute —
  <https://docs.nvidia.com/nsight-compute/NsightCompute/index.html>
- PyTorch Profiler —
  <https://pytorch.org/docs/stable/profiler.html>
- NVIDIA DCGM —
  <https://docs.nvidia.com/datacenter/dcgm/latest/user-guide/index.html>
- `py-spy` —
  <https://github.com/benfred/py-spy>
- NVIDIA Triton Inference Server `genai-perf` —
  <https://github.com/triton-inference-server/perf_analyzer/tree/main/genai-perf>
- vLLM benchmarking guidance —
  <https://github.com/vllm-project/vllm/tree/main/benchmarks>

### Kernel-level work

- Dao et al., *FlashAttention*, 2022 —
  <https://arxiv.org/abs/2205.14135>
- Dao, *FlashAttention-2*, 2023 —
  <https://arxiv.org/abs/2307.08691>
- Shah et al., *FlashAttention-3*, 2024 —
  <https://arxiv.org/abs/2407.08608>
- NVIDIA Transformer Engine —
  <https://docs.nvidia.com/deeplearning/transformer-engine/user-guide/index.html>
- Triton language tutorials —
  <https://triton-lang.org/main/getting-started/tutorials/index.html>
- Lin et al., *AWQ*, 2023 —
  <https://arxiv.org/abs/2306.00978>
- Frantar et al., *GPTQ*, 2022 —
  <https://arxiv.org/abs/2210.17323>
- Xiao et al., *SmoothQuant*, 2023 —
  <https://arxiv.org/abs/2211.10438>

### Framework-level work (inference)

- Yu et al., *Orca: A Distributed Serving System for
  Transformer-Based Generative Models*, OSDI 2022 —
  <https://www.usenix.org/conference/osdi22/presentation/yu>
- Kwon et al., *Efficient Memory Management for Large Language
  Model Serving with PagedAttention*, SOSP 2023 —
  <https://dl.acm.org/doi/10.1145/3600006.3613165>
- vLLM documentation —
  <https://docs.vllm.ai/en/latest/>
- HuggingFace Text Generation Inference (TGI) —
  <https://github.com/huggingface/text-generation-inference>
- NVIDIA TensorRT-LLM —
  <https://github.com/NVIDIA/TensorRT-LLM>
- Leviathan et al., *Speculative Decoding*, 2023 —
  <https://arxiv.org/abs/2211.17192>
- Chen et al., *Accelerating LLM Inference with Speculative
  Sampling*, 2023 — <https://arxiv.org/abs/2302.01318>
- Cai et al., *Medusa*, 2024 —
  <https://arxiv.org/abs/2401.10774>
- Li et al., *EAGLE*, 2024 —
  <https://arxiv.org/abs/2401.15077>

### Framework-level work (training)

- Rajbhandari et al., *ZeRO*, SC 2020 —
  <https://arxiv.org/abs/1910.02054>
- PyTorch FullyShardedDataParallel (FSDP) —
  <https://pytorch.org/docs/stable/fsdp.html>
- PyTorch `torch.compile` —
  <https://pytorch.org/docs/stable/torch.compiler.html>

### System-level work

- NVIDIA NCCL environment variables —
  <https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html>
- NCCL tests —
  <https://github.com/NVIDIA/nccl-tests>
- NVIDIA Multi-GPU topology / NVLink —
  <https://docs.nvidia.com/datacenter/tesla/index.html>
- Linux `numactl` / `numastat` —
  <https://man7.org/linux/man-pages/man8/numactl.8.html>

### In-track cross-references

- `modules/mod-501-technical-strategy/SOLUTION.md` — metric
  choice as a strategy decision.
- `modules/mod-503-cross-org-initiative/SOLUTION.md` —
  coordinating with model, infra, FinOps teams whose systems
  you are touching.
- `modules/mod-505-long-term-technical-bets/SOLUTION.md` —
  picking FP8, MoE serving, speculative decoding as durable
  multi-year bets.
- `projects/project-03-performance-optimization/` (in the
  paired learning repo) — the project README, requirements,
  architecture, rubric, and deliverables spec this solution
  is written against.

### Practitioner reference

Per this track's source policy, VeriSwarm and similar
practitioner-level write-ups may be used as **implementation
examples** but not as standards authorities. When a candidate
cites a vendor blog post or a community benchmark, it must be
labeled as such in the design doc, and the load-bearing
methodology claim must come from one of the sources above.
