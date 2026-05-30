# SOLUTION — Project 01: Company-Wide Distributed Training Framework

> Read this *after* you have attempted the project in
> [`ai-infra-principal-engineer-learning/projects/project-01-distributed-training`](https://github.com/ai-infra-curriculum/ai-infra-principal-engineer-learning/tree/main/projects/project-01-distributed-training).
> The implementation depth (framework code, chaos scripts, Grafana JSON)
> lives in the engineer and senior-engineer tracks. The job of this
> document is to defend the **principal-engineer judgment calls** the
> project is grading you on.

The project (`README.md`, `requirements.md`, `architecture.md`,
`STEP_BY_STEP.md`, `rubric.md`, `deliverables/README.md` in the paired
learning repo) is a 100-hour design-and-build exercise for the
principal IC who owns "how every ML team in the company trains
models." The reference deliverable set is 18 artifacts spanning a
15-25 page design doc, six ADRs, a working framework, chaos evidence,
a migration plan, a cost model, and a recorded tech talk.

This SOLUTION.md does **not** ship that framework code. It walks the
six load-bearing decisions the rubric tests, gives a defensible worked
answer to each, lists how a reviewer mechanically checks the project,
and catalogues the failure modes graders see repeatedly.

---

## 1. Solution Overview

### 1.1 What the project is actually grading

The paired `rubric.md` distributes 100 points across six dimensions
with explicit Level 0-4 evidence ladders:

| Dimension | Weight | Anchor in `rubric.md` |
|-----------|-------:|----------------------|
| Technical depth & correctness | 25 | §"Dimension 1" |
| Fault tolerance & operations | 20 | §"Dimension 2" |
| Design doc & ADRs | 20 | §"Dimension 3" |
| Cross-team adoption / migration | 15 | §"Dimension 4" |
| Cost / business framing | 10 | §"Dimension 5" |
| Communication (tech talk + writing) | 10 | §"Dimension 6" |

55 of those 100 points (Dimensions 3, 4, 5, 6) are **non-code**. That
ratio is the project's main pedagogical signal: at principal level the
framework code is necessary but not sufficient — the judgment around
the code is the thing being assessed.

The rubric also publishes a calibration that matters when you score
yourself before submission: Level 3 is "publishable internally at a
real company"; Level 4 is "the artifact a principal engineer would
link from their promo packet." Hours spent does not move the score;
only outcome does (`rubric.md` §"Calibration Notes for Reviewers").

### 1.2 What a passing solution looks like in one paragraph

A passing solution picks **one** primary sharding stack (FSDP), keeps
**one** documented secondary path (DeepSpeed ZeRO-3), ships a
**sharded, manifest-gated, async-uploaded** checkpoint format,
integrates with **two** cluster backends (Kubernetes operator
mandatory; Slurm agent recommended), exposes the metric set listed in
`architecture.md` §7 via Prometheus + Grafana, survives the **ten**
chaos scenarios in `STEP_BY_STEP.md` Day 19 with documented recovery
behavior, and is defended by a **15-25 page design doc plus six ADRs**
reviewed by 3+ engineers. The cost model and migration plan exist as
first-class artifacts, not appendices.

A failing solution is almost always one of: a beautiful framework
nobody wants (skipped stakeholder discovery), an ambitious 3D
parallelism stack that never converged (over-bet on Megatron on day
one), or a happy-path demo with no chaos evidence
(`STEP_BY_STEP.md` §"Common Failure Modes" enumerates the rest).

### 1.3 How this document is organised

§2 walks the worked answer for each of the six load-bearing
decisions. §3 maps the reviewer's mechanical checks back to evidence
the submission must provide. §4 is a reusable grading rubric — the
project's published rubric expanded with per-line evidence prompts.
§5 catalogues common mistakes. §6 collects the official references.

---

## 2. Worked Answer — The Six Load-Bearing Decisions

The project's `architecture.md` enumerates a "decision menu"
(`architecture.md` §9) and tells you that six of them must be written
up as ADRs (`adr/0001` … `adr/0006`). The worked answer below is one
defensible position per decision. It is **not** the only defensible
answer; the grader is looking for the *quality of the defence*, not a
specific stack pick. Each subsection follows the structure the rubric
expects in an ADR: context, decision, alternatives considered,
consequences accepted.

### 2.1 ADR 0001 — Primary sharding strategy: FSDP with ZeRO-3 as documented fallback

**Context.** The framework must support models from 1 B to 1 T
parameters and run on 100-1,000 GPUs per job
(`requirements.md` §1.2, M-FR-5/6/7/8). The shape of the project's
decision tree (`architecture.md` §3) covers DDP, FSDP, ZeRO-3, FSDP +
TP, and full 3D parallelism. Picking one *primary* and one
*documented secondary* is the rubric's Dimension 1 Level 3 evidence
ladder.

**Decision.** Make **PyTorch FSDP** the primary, default sharding
stack. Keep **DeepSpeed ZeRO-3** as a documented secondary for the
narrow band of models that hit a memory wall under FSDP. Defer
Megatron-style tensor parallelism and pipeline parallelism until a
real workload requires them; surface them in the decision tree but do
not ship them in v1.

**Why FSDP first**: it is in-tree in PyTorch, tracks PyTorch upstream
releases without a separate runtime, composes with `torch.compile`,
and uses the same `nn.Module` user code as DDP — so the migration
path from a hand-rolled `torchrun` script is "wrap your model with
`FullyShardedDataParallel` and run on our cluster" rather than "port
to a different framework" (PyTorch FSDP docs, see §6.1). The FSDP
sharding strategies (`FULL_SHARD`, `HYBRID_SHARD`, `SHARD_GRAD_OP`)
plus `BackwardPrefetch.BACKWARD_PRE` give the comms/compute overlap
controls a principal will need when tuning MFU.

**Why ZeRO-3 stays in the box.** DeepSpeed ZeRO-3 shards parameters,
gradients, and optimizer state across data-parallel ranks and was the
sharding stack that first made multi-billion-parameter training
practical on commodity clusters (DeepSpeed ZeRO docs, see §6.2). It
remains the credible fallback for two cases: (a) memory-tight
fine-tunes where ZeRO-Infinity's CPU/NVMe offload buys you a model
that won't otherwise fit, and (b) teams already on DeepSpeed whose
migration cost to FSDP is not justified by v1 of this framework.

**Alternatives considered**:

- **Megatron-LM 3D parallelism as primary.** Rejected for v1. The
  user surface is heavier (model code must be expressed in Megatron's
  layer abstractions); the operational surface is larger (separate
  comms libs and rendezvous); the percentage of company workloads at
  principal scope that actually need TP × PP × DP is small. Keep it
  on the decision tree (`architecture.md` §3, branch ≥ 500 B) and
  ship it only when the first workload that needs it lands.
- **DeepSpeed ZeRO-3 as primary.** Defensible, and the right pick for
  an org whose existing momentum is already on DeepSpeed. Rejected
  here because the project's stated goal is convergence across
  multiple teams onto one stack, and the FSDP-first path imposes the
  smaller user-code delta on the typical workload.
- **Build a sharding library in-house.** Rejected. Sharding is one of
  the worst places to spend innovation budget — the heuristic in
  `architecture.md` §9 ("boring choices are correct choices") applies.

**Consequences accepted**:

- A workload that needs Megatron-style TP for activations memory will
  surface as the first credible motivator to add TP, and the team
  will absorb that engineering cost when it does. Document the
  trigger criterion in the ADR ("ADR re-opens when activation memory
  is dominated by a single attention layer that no longer fits with
  ZeRO-3 + activation checkpointing").
- DeepSpeed ZeRO-3 path will have a second config file format and a
  second test matrix; budget the maintenance.
- FSDP defaults change between PyTorch versions
  (`STEP_BY_STEP.md` Day 10 gotcha); pin the PyTorch version in
  `requirements.txt` and document the pin's expiry policy.

**How a reviewer scores it.** Open ADR 0001. The decision must name
both the primary and the secondary, with model-size boundaries from
the decision tree. The "Alternatives Considered" section must
explicitly cover Megatron and DeepSpeed-as-primary. The "Consequences
Accepted" section must contain at least one item that names a future
workload that re-opens the decision.

### 2.2 ADR 0002 — Checkpoint format: sharded + manifest-gated, async uploader, FQN-keyed

**Context.** The fault-tolerance requirements (`requirements.md` §1.3,
M-FR-12 through M-FR-17) demand checkpoints that are async, sharded,
resumable on a different topology, and detected as "good" by a
written manifest rather than `mtime`. The cost requirement (M-NFR-2:
checkpoint write overhead ≤ 2 % of step time amortized) and the
RTO/RPO requirements (`architecture.md` §5) constrain the design
space.

**Decision.** Each rank writes a sharded checkpoint to **local NVMe**
synchronously, keyed by **parameter FQN** so it can be loaded onto a
different world size. An **async uploader sidecar** pushes shards to
S3-compatible object storage. The checkpoint becomes "good" only when
the **manifest is written last** with `{world_size, step,
shard_sha256[]}`. Reuse PyTorch's `torch.distributed.checkpoint`
(DCP) for the sharded state-dict primitives; do not roll your own
serializer (see PyTorch DCP docs, §6.3).

**Why FQN-keyed instead of rank-indexed.** Rank-indexed checkpoints
embed the world size into the format and force a re-shard step on
resume. FQN-keyed checkpoints store per-parameter state under the
fully qualified module name, letting FSDP's `load_sharded_state_dict`
re-shard on load to any topology that satisfies the divisibility
constraints. This is the requirement that buys you M-FR-13
(resumable to different topology).

**Why manifest written last.** A torn checkpoint without the manifest
is invisible to the resume path, which is the correct behaviour — the
job falls back to the previous good ckpt
(`architecture.md` §5, "Bad checkpoint" row). The integrity check
must verify SHA-256 over each shard listed in the manifest, not
existence alone.

**Alternatives considered**:

- **`torch.save(model.state_dict())` on rank 0.** This is the
  Dimension 2 Level 1 anti-pattern in the rubric. It consolidates
  state on a single rank (memory blow-up for large models), serialises
  the write, and cannot represent topology-flexible state. Useful
  only for export, never for in-flight checkpointing.
- **Synchronous upload to S3, no local NVMe staging.** Couples
  training step latency to network throughput tail. Rejected; an
  async two-stage write (NVMe → S3 via sidecar) absorbs the tail.
- **Rolling your own format on top of `pickle`.** Rejected. PyTorch
  DCP already gives you sharded planners, distributed I/O backends,
  and resume-on-different-topology semantics. The "rolling your own"
  argument is only credible if you can name a specific upstream
  limitation; the ADR must state one if it makes that choice.

**Consequences accepted**:

- Async upload introduces a **double-failure** window: a node lost
  before its local shard finishes uploading loses that shard.
  Mitigation: keep last *N* checkpoints across all ranks' local NVMe
  and treat any not-yet-uploaded ckpt as best-effort. Document this
  in the failure-mode analysis (this is the "non-obvious case" the
  Dimension 2 Level 4 ladder rewards).
- The sharded format is not `torch.load`-able directly. Honour C-6
  (`requirements.md` §3) by shipping an **export step** that
  consolidates to a flat state-dict for downstream consumers.
- Manifest validation costs one extra S3 round-trip per checkpoint
  interval; this is well below the 2 % step-time budget.

### 2.3 ADR 0003 — Cluster manager abstraction: thin per-backend agent behind a single CRD-shaped contract

**Context.** M-FR-2 demands the same job spec runs unchanged on at
least two cluster backends, with Kubernetes mandatory and a strongly
recommended second (Slurm, Ray, or another K8s region/account).
`architecture.md` §2.2 prescribes a **per-cluster agent** that
materialises workers but does not know about FSDP, checkpoints, or
model code.

**Decision.** Build a **CRD + Operator** on Kubernetes (the
idiomatic K8s shape) and a **long-running translator agent** on
Slurm that converts the same `JobSpec` into `sbatch` invocations.
The control plane talks to backends through a `ClusterBackend`
interface that exposes only `submit`, `cancel`, `status`, and
`stream_logs`. The runtime (FSDP wrap, checkpoint, metrics) lives
inside the worker process and is completely unaware of which backend
launched it.

**Why CRD + Operator on K8s.** It is the idiomatic K8s integration
shape (custom resource representing the user intent, controller
reconciling to that intent). It composes with `kubectl`, RBAC,
admission webhooks, and the rest of the K8s ecosystem the platform
team already operates. The reference path is to wrap or model after
the Kubeflow Training Operator's `PyTorchJob` CRD shape (see §6.4).

**Why not Karmada / KubeFed.** Multi-cluster glue from Karmada is
attractive but takes a hard dependency on a federation runtime that
the platform team will then own. The control plane being responsible
for its own placement decisions is the choice that preserves
optionality on the cluster backend (the project's
`architecture.md` §9 alternative-considered row says the same).

**Alternatives considered**:

- **Helm + raw K8s `Job`s.** Loses the lifecycle controller —
  retries, preemption handling, status reconciliation all leak into
  client-side scripts. Rejected.
- **Karmada or KubeFed-driven multi-cluster.** Adds an org-level
  dependency that may not be paid for. Rejected for v1; revisit
  when there are more than two clusters in scope.
- **One backend only (K8s).** Defensible but fails M-FR-2 and
  forecloses the Slurm-on-prem use case that the architecture
  document calls out as a real adopter pattern.

**Consequences accepted**:

- Adding a third backend (Ray, Nomad, raw VMs) means writing a third
  agent. The `ClusterBackend` interface is the contract that keeps
  this an N-day project, not an N-week one. Test it with a stub
  backend in CI.
- The Slurm translator must own credential handoff (no shared
  service-account keys; `requirements.md` M-FR-29).

### 2.4 ADR 0004 — Comms backend: NCCL on NVIDIA, Gloo as CPU fallback

**Context.** Collective communication dominates training step time
for sharded workloads. The framework needs a comms backend that is
the industry default and that the worker pods can rely on.

**Decision.** **NCCL** on NVIDIA hardware, **Gloo** on CPU as the
fallback path used by tests and tiny CPU-only smoke jobs. UCC / MSCCL
are listed on the decision menu as future work; do not ship them in
v1.

**Why NCCL.** NCCL is the in-tree backend for distributed PyTorch
training on NVIDIA GPUs (PyTorch distributed docs, §6.5) and is the
backend FSDP, DeepSpeed, and Megatron all target. The NCCL
documentation (§6.6) covers topology detection (NVLink, NVSwitch,
InfiniBand, RoCE, sockets) and tuning environment variables;
`NCCL_DEBUG=INFO` is the canonical first thing to enable on a new
fabric (`STEP_BY_STEP.md` Phase 0 gotcha).

**Why Gloo as a fallback rather than as default.** Gloo is the
canonical CPU backend in PyTorch distributed and exists to make tests
and CI runnable without GPUs. It is not a credible production
backend for the workloads this framework targets.

**Alternatives considered**:

- **UCC / MSCCL.** Interesting for cross-vendor environments
  (MSCCL allows custom collective algorithms). Defer; add behind an
  experimental flag.
- **MPI directly.** Rejected; PyTorch's distributed abstraction
  already chooses NCCL for CUDA tensors and Gloo for CPU. Adding MPI
  buys nothing for the workloads in scope.

**Consequences accepted**:

- The framework's interconnect-requirement declaration (C-5,
  `requirements.md` §3) is enforced by inspecting NCCL's topology
  output at job init; jobs that need InfiniBand and find sockets
  must refuse to start, not silently degrade.
- NCCL silently falls back to TCP if it cannot find IB/RoCE/NVLink
  — `STEP_BY_STEP.md` Phase 0 calls this out. Always log
  `NCCL_DEBUG=INFO` for the first run on any new environment, and
  emit a startup banner that prints the chosen transport.

### 2.5 ADR 0005 — Observability schema: Prometheus exposition is the public API, Grafana JSON is the contract

**Context.** Once teams build dashboards and alerts on the metric and
log schema, you cannot change it
(`architecture.md` §7 calls it a public API). The mandatory metric
and log shapes are fixed in `architecture.md` §7 and
`requirements.md` §1.5. The framework must ship Grafana dashboards
that load in < 3 s for jobs up to 1,024 ranks
(`requirements.md` §2.5).

**Decision.** Expose every metric in the project's mandatory list via
the **Prometheus exposition format** with the **mandatory label set**
(`job_id`, `team`, `cluster`, `rank`, `world_size`, `model_family`,
`parallelism`). Ship **four Grafana dashboards** as version-controlled
JSON in `monitoring/grafana/` (Job overview, Cluster overview, Fleet
overview, Postmortem dashboard, per `architecture.md` §7). Use
**OpenTelemetry** for tracing only, not metrics, until OTel's metrics
SDK is fully landed across the PyTorch ecosystem.

**Why Prometheus + Grafana + Loki.** Industry default; every infra
team already runs it; the migration cost for an adopting team is
near-zero. The mandatory label set lets the Fleet dashboard answer
"who is using the framework, on which cluster, for which model
family" in a single PromQL.

**Why DCGM-sourced GPU metrics.** GPU SM utilisation and HBM usage
cannot be inferred reliably from `nvidia-smi` or PyTorch; the NVIDIA
DCGM exporter is the right source (NVIDIA DCGM docs, §6.7). It
requires the NVIDIA GPU operator's monitoring component
(`STEP_BY_STEP.md` Day 14 gotcha); do not assume it is installed.

**Alternatives considered**:

- **OpenTelemetry metrics only.** Defer. The PyTorch ecosystem's
  OTel metrics integration is still maturing; betting on it now
  trades a real "boring" choice for a future one.
- **Push to a vendor (Datadog, Honeycomb, Grafana Cloud).** Reasonable
  for a specific org with a Datadog mandate, but it pins a 3-year
  decision to a vendor — explicitly anti-listed in the track's
  `SOLUTION_OVERVIEW.md`.

**Consequences accepted**:

- The metric and label set is **frozen** as soon as the first adopter
  builds a dashboard on it. Treat schema changes as semver-major
  events with a deprecation window.
- DCGM exporter dependency is a deployment prerequisite; the
  Getting Started doc must list it.

### 2.6 ADR 0006 — Security boundary: identity at the API, scope at the job, secrets at the orchestrator

**Context.** The security requirements (`requirements.md` §1.7,
M-FR-29/30/31) and the architecture's §10 sketch demand federated
identity, per-job IAM scoping, and orchestrator-injected secrets. The
threat model is **insider misconfiguration first, malicious code in
training scripts second** — the framework must keep a sloppy job spec
from exfiltrating another team's dataset.

**Decision.** Authenticate users at the **submission API via OIDC**.
Mint a **short-lived per-job token** (12 h default) scoped to (read:
datasets in spec, write: ckpt path in spec). Workers exchange this
for cloud credentials via the cluster's workload identity provider
(IRSA on EKS, GKE Workload Identity, AKS Pod Identity, Vault on
on-prem). Containers run as **non-root with read-only root FS and all
capabilities dropped** except those explicitly required.

**Why federated OIDC.** Avoids the long-lived service-account-key
trap. The cluster's workload identity provider is the right
abstraction because it already integrates the cloud's IAM with K8s
service accounts (AWS IRSA docs and the equivalent for GCP and
Azure, §6.8).

**Alternatives considered**:

- **Long-lived per-team service-account keys in the job spec.**
  Explicitly forbidden by M-FR-31 and is the wrong shape: a leaked
  spec is a credential leak.
- **Run training as root.** Forbidden by C-4
  (`requirements.md` §3). Cite the K8s security context
  documentation (§6.9) when defending the non-root choice; it is
  the boring, correct answer.

**Consequences accepted**:

- The framework cannot debug a job by `kubectl exec`-ing as root.
  Use ephemeral debug containers
  (`kubectl debug --image=...`) per K8s documentation, or surface a
  per-job sidecar debug image.
- Cross-cluster credential handoff in the multi-cluster failure case
  (`architecture.md` §6) is the hardest part of this ADR; the open
  question (`architecture.md` §12, item 4) must be resolved
  explicitly — typically by minting a fresh per-cluster token on
  migration rather than carrying credentials across.

---

## Implementation

The six ADRs above are the *decisions*; this section names the
*artifacts* a passing submission ships and how they line up with the
repo layout demanded by `deliverables/README.md`
§"Repository Layout (Mandatory)". It is a map, not a tutorial — the
mechanical "did you build it?" check happens in §3 below.

- **Control plane (`mlctl/`, `controller/`).** A `mlctl submit` CLI
  that posts a `TrainingJob` spec to a single controller; the
  controller validates the spec (S-NFR-10), persists it to the state
  store, and reconciles toward "running". This is the surface that
  ADR 0003 abstracts over Kubernetes/Slurm/cloud-batch backends.
- **Per-backend agents (`agents/k8s/`, `agents/slurm/`,
  `agents/batch/`).** Thin translators from the CRD-shaped contract
  to the backend's native job object. Each agent owns *only* launch,
  status, and teardown — no scheduling logic — per ADR 0003.
- **Training runtime (`runtime/`).** The FSDP/ZeRO-3 wrapper from
  ADR 0001, the sharded-checkpoint writer and manifest gate from
  ADR 0002, and the NCCL/Gloo selector from ADR 0004. The reference
  7B job under `examples/reference-7b.yaml` exercises this path
  end-to-end.
- **Observability (`obs/`).** A Prometheus exporter publishing the
  metric names ADR 0005 freezes as the public contract, plus the
  Grafana JSON dashboards that `make demo` provisions. The chaos
  drill from §3.4 reads from these same metrics — there is no second
  telemetry path.
- **Security plane (`auth/`, `policy/`).** Workload-identity token
  exchange at the API edge, per-job scoped credentials minted by the
  orchestrator, and the per-cluster re-mint on migration that
  ADR 0006 calls out as load-bearing.
- **Glue (`Makefile`, `examples/`, `docs/adr/`).** The three
  load-bearing `make` targets (`demo`, `chaos`, `test`) and the six
  ADR markdown files. These are not optional — §3.1 and §3.6 below
  fail the submission outright if they are missing or mis-named.

What is deliberately *not* an implementation deliverable: a custom
scheduler, a bespoke comms library, or a new checkpoint format. Each
of those would be a red flag against ADR 0001/0003/0004 and is
called out in §5 ("Common Mistakes").

---

## 3. Validation Steps — How a Reviewer Mechanically Checks the Submission

The validation list below is the merged set from
`requirements.md` §6 ("Acceptance Criteria"), `STEP_BY_STEP.md`
("Validation Gates" and "Final Checklist Before Submitting"), and
`deliverables/README.md` ("What You Will Be Asked at Review"). Use it
as a self-check before submission *and* as the runbook a reviewer
will actually follow.

### 3.1 Repo discoverability (≤ 5 minutes)

1. `git clone` the repo and read the top-level `README.md`. Within
   2 minutes the reviewer must understand what the framework does and
   does not do
   (`deliverables/README.md` §"What You Will Be Asked at Review", item 1).
2. The repo layout must match `deliverables/README.md`
   §"Repository Layout (Mandatory)" — file names matter; mis-naming
   is called out as "delays review".
3. The `Makefile` exposes the three load-bearing targets:
   `make demo`, `make chaos`, `make test`.

### 3.2 Demo reproducibility (≤ 30 minutes)

4. `mlctl submit examples/reference-7b.yaml` runs end-to-end from a
   fresh clone in under 30 minutes
   (`STEP_BY_STEP.md` "Final Checklist", item 1).
5. State transitions visible in the state store (`queued → running →
   succeeded`), and the job spec validator surfaces a clear
   suggested-fix error for at least one intentionally bad spec
   (S-NFR-10).
6. `mlctl status <job-id>` shows live progress; the Job-overview
   Grafana dashboard shows the job within 60 s
   (`requirements.md` §6, criterion 2).

### 3.3 Reference workload + MFU (≤ 60 minutes including setup)

7. The bundled 7B reference job converges within the documented
   wall-clock and budget (`requirements.md` §6, criterion 1).
8. MFU matches the README claim within ±2 percentage points
   (`requirements.md` §6, criterion 3). Required floor: ≥ 45 % on
   H100 at 32 GPUs for 7B, ≥ 40 % at 256 GPUs for 70B, with
   hardware-adjusted equivalents documented for non-H100
   environments (M-NFR-1).
9. `benchmarks/optimization-log.md` contains at least five rows with
   measured before/after numbers and an attribution per row
   (`deliverables/README.md` item 7; Dimension 1 Level 4 in the
   rubric requires this).

### 3.4 Fault tolerance (≤ 60 minutes)

10. `scripts/chaos/run_all.sh` executes all **ten** scenarios listed
    in `STEP_BY_STEP.md` Day 19 with documented expected behaviour
    (criterion 4). The ten scenarios are: pod kill, node drain,
    NCCL hang, OOM, disk full, bad checkpoint, slow node, network
    partition, host reboot, container OOM-kill.
11. Killing a worker pod mid-training causes auto-recovery in
    ≤ 10 min at 8 GPUs (criterion 5) — extrapolated target documented
    for 64+ GPUs.
12. A spot-preemption simulation (SIGTERM with 2-min grace) produces
    a clean checkpoint and a successful resume (criterion 6;
    `architecture.md` §5 spot-preemption row).
13. `docs/chaos-report.md` records before/after state per scenario,
    not just pass/fail.
14. `docs/oncall.md` contains at least eight incident playbooks
    (rubric Dimension 2 Level 3 evidence).

### 3.5 Multi-cluster (≤ 30 minutes)

15. The same job spec submitted with `--cluster=cluster-a` and
    `--cluster=cluster-b` runs on each, identically (criterion 7).
16. A placement-policy demo shows the scheduler choosing between two
    clusters based on queue depth or price (criterion 8). At minimum,
    the policy chain `region-pin → cheapest → lowest-queue`
    (`architecture.md` §6) is exercised in a smoke test.

### 3.6 Design artifacts (≤ 60 minutes of reading)

17. `docs/design-doc.md` is 15-25 pages, covers every section in the
    Week 1 outline (`STEP_BY_STEP.md` Day 2), and has three or more
    named reviewers acknowledged with their comments addressed
    (criterion 9).
18. Six ADRs (at minimum) exist, each ≥ 1 page, each naming the
    alternatives considered and the consequences accepted (criterion
    10; rubric Dimension 3 Level 3).
19. `docs/migration-plan.md` covers three teams; each entry has
    current state, numbered migration steps, rollback plan, success
    metric, owner, reviewer, and estimated effort (criterion 11;
    rubric Dimension 4 Level 3).
20. `docs/cost-model.{xlsx,ipynb}` separates assumptions from outputs
    (criterion 12; rubric Dimension 5 Level 3) and has an executive
    summary a non-engineer can read in one paragraph.

### 3.7 Code-quality bar

21. Test coverage ≥ 80 % on `control/`, `runtime/checkpoint/`, and
    `shared/`; CI green on every PR (M-NFR-11, criterion 13).
22. `mypy --strict` clean on the public API surface (M-NFR-12,
    criterion 14).
23. No file > 800 LOC; no function > 50 LOC; no nesting deeper than
    four levels (M-NFR-13, criterion 15).

### 3.8 Communication

24. Tech talk recorded, 25-45 minutes, audio mandatory, video
    optional (criterion 16).
25. Slide deck present in `talks/slides.pdf` (criterion 17).
26. `docs/getting-started.md` gets a stranger to a submitted job in
    ≤ 30 minutes (criterion 18; S-NFR-9).
27. `docs/troubleshooting.md` has ≥ 10 entries
    (`deliverables/README.md` item 17; rubric Dimension 6 Level 3).

### 3.9 Two qualitative checks the rubric prizes

28. The design doc's failure-model table (per `architecture.md` §5)
    is findable in ≤ 30 seconds. This is the most-asked-about
    artifact in real principal design reviews; reviewers grade
    findability, not just presence
    (`deliverables/README.md` §"What You Will Be Asked at Review",
    item 3).
29. At least one ADR explicitly captures a decision the author would
    reconsider, with the trigger condition named. This is the
    Dimension 3 Level 4 evidence ladder and the most reliable signal
    of principal-level judgment in the submission.

---

## 4. Rubric / Review Checklist

The project's published `rubric.md` is the canonical scoring
instrument. Below is the expansion principal-level graders use during
the actual read-through, dimension by dimension. Treat it as the
checklist to self-assess before submission (the rubric's
`§"Self-Assessment Before Submission"` instruction is to fix any
sub-70 dimension before sending it out).

### 4.1 Dimension 1 — Technical Depth & Correctness (25 pts)

| Check | Where to find evidence | Level signal |
|-------|------------------------|--------------|
| FSDP integration works on a single node | `examples/tiny-gpt/` runs via `make demo` | Level 2 floor |
| FSDP **and** at least one of ZeRO-3 / TP / MoE integrated | `src/training_framework/runtime/parallelism/` + selector logic | Level 3 floor |
| Reference 7B converges at ≥ 32 GPUs with ≥ 40 % MFU (hw-adjusted ok) | `benchmarks/reference-7b.md` (loss curve PNG + MFU table) | Level 3 floor |
| Sharding decision tree documented in ADR 0001 with model-size boundaries | `adr/0001-sharding-strategy.md` | Level 3 floor |
| Three or more parallelism strategies selectable per spec | Runtime selector + reference workloads | Level 4 |
| MFU on H100 ≥ 50 %; optimization log ≥ 5 measured changes with attribution | `benchmarks/optimization-log.md` | Level 4 |
| Reference 70B converges (or simulated equivalent with credible math) | `benchmarks/reference-70b-*.md` | Level 4 |

### 4.2 Dimension 2 — Fault Tolerance & Operations (20 pts)

| Check | Evidence | Level signal |
|-------|----------|--------------|
| Sharded async checkpointing with auto-restart on pod failure | `runtime/checkpoint/` code + ≥ 1 chaos test run | Level 2 floor |
| All 10 chaos scenarios pass with documented recovery behaviour | `scripts/chaos/run_all.sh` output + `docs/chaos-report.md` | Level 3 floor |
| Spot preemption: SIGTERM → ckpt → requeue end-to-end | Chaos scenario 3 + design-doc §"Failure Model" row | Level 3 floor |
| MTTR ≤ 10 min @ 32 GPU on node loss | `docs/chaos-report.md` row for node-drain scenario | Level 3 floor |
| `docs/oncall.md` with ≥ 8 incident playbooks | `docs/oncall.md` | Level 3 floor |
| Topology-flexible resume (different world size) demonstrated | `scripts/chaos/topology_resume_*.sh` + checkpoint format ADR | Level 4 |
| Cross-cluster migration on region outage demonstrated | `scripts/chaos/region_failover_*.sh` | Level 4 |
| Failure-mode analysis covers slow node, partial spot loss, control-plane partition | Design-doc appendix | Level 4 |

### 4.3 Dimension 3 — Design Doc & ADRs (20 pts)

| Check | Evidence | Level signal |
|-------|----------|--------------|
| Design doc 15-25 pages covering all `STEP_BY_STEP.md` Week 1 sections | `docs/design-doc.md` | Level 3 floor |
| 6 ADRs, each with context + decision + alternatives + consequences + status | `adr/0001` … `adr/0006` | Level 3 floor |
| 3+ named reviewers, comments addressed | Front-matter of design doc; PR review thread | Level 3 floor |
| At least one ADR captures a decision the author would reconsider, with trigger condition | Any ADR's "Consequences"/"Open" section | Level 4 |
| Failure-model section is reference-quality | `docs/design-doc.md` §"Failure Model" | Level 4 |
| Design doc publishable as external tech blog | Overall readability + structure | Level 4 |

### 4.4 Dimension 4 — Cross-Team Adoption / Migration (15 pts)

| Check | Evidence | Level signal |
|-------|----------|--------------|
| Migration plan for 3 named teams with current state + steps + rollback + owner + metric + effort | `docs/migration-plan.md` | Level 3 floor |
| Stakeholder interview notes for ≥ 3 teams | `docs/stakeholder-interviews.md` | Level 3 floor |
| At least one signed-off review from a hypothetical or real adopter | Migration plan annotation | Level 3 floor |
| One adopting team actually migrated end-to-end (real or in-exercise) | Adopter retro doc | Level 4 |
| Migration plan covers cultural / political factors (e.g., namespace veto) | Per-team migration entry | Level 4 |

### 4.5 Dimension 5 — Cost / Business Framing (10 pts)

| Check | Evidence | Level signal |
|-------|----------|--------------|
| Cost model with separated assumptions and outputs | `docs/cost-model.{xlsx,ipynb}` | Level 3 floor |
| 12-month savings projection defensible to finance | Cost model + design-doc summary | Level 3 floor |
| Executive summary ≤ 1 paragraph readable by a non-engineer | Top of cost-model doc | Level 3 floor |
| Per-team budget enforcement implemented in code | `src/training_framework/control/budget.py` + tests | Level 3 floor |
| Conservative / base / aggressive scenarios + sensitivity analysis on spot mix, MFU, GPU price | Cost model sheets / cells | Level 4 |
| Framework auto-emits cost telemetry | Job records GPU-hours + $ + storage cost (M-FR-27) | Level 4 |

### 4.6 Dimension 6 — Communication (Tech Talk + Writing) (10 pts)

| Check | Evidence | Level signal |
|-------|----------|--------------|
| Talk recorded, 25-45 min, audio comprehensible | `talks/tech-talk.{mp4,url}` | Level 2 floor |
| Talk structured per `STEP_BY_STEP.md` Day 23 outline | Talk + slides | Level 3 floor |
| README opinionated; getting-started gets a stranger running in ≤ 30 min | `README.md` + `docs/getting-started.md` | Level 3 floor |
| `docs/troubleshooting.md` with ≥ 10 entries | `docs/troubleshooting.md` | Level 3 floor |
| Talk submittable to external venue; includes "what I'd do differently" slide grounded in observed behavior | Talk content + slide | Level 4 |
| Voice across docs is consistent, tight, fluff-free | Sample of docs | Level 4 |

### 4.7 Scoring sheet

Fill the worksheet from `rubric.md`. Pass at ≥ 70; portfolio-grade at
≥ 85. The rubric's calibration note is binding:

> Score on **outcome**, not hours. A common error: scoring "Level 4"
> for high effort that didn't actually produce the artifact (e.g.,
> "spent 30 hours on chaos but only 3 scenarios pass").
> (`rubric.md` §"Calibration Notes for Reviewers")

---

## 5. Common Mistakes

The list below merges `STEP_BY_STEP.md` §"Common Failure Modes" with
the failure patterns that the principal-engineer track's
`SOLUTION_OVERVIEW.md` documents and the rubric calibration notes.
Each entry names the failure, the rubric dimension(s) it costs you,
and the corrective action.

1. **Started coding before designing.** Spent week 1 on K8s plumbing;
   ended up rewriting half of it in week 4 because the failure model
   wasn't clear. (`STEP_BY_STEP.md` §"Common Failure Modes", item 1.)
   *Costs:* Dimensions 1, 3. *Fix:* draft the design doc and failure
   model first; gate Week 2 on a design-doc review.

2. **Over-bet on Megatron-style 3D parallelism on day 1** because it
   sounded principal-level. Burned weeks; right move is FSDP first,
   add TP later only when a workload requires it.
   (`STEP_BY_STEP.md` item 2.) *Costs:* Dimensions 1, 4.
   *Fix:* defer TP behind a named workload-driven trigger in ADR 0001.

3. **Skipped the chaos suite.** "It works on the happy path" — every
   framework does; the chaos suite is the differentiator
   (`STEP_BY_STEP.md` item 3). *Costs:* Dimension 2 (the entire 20
   points). *Fix:* gate `make chaos` in CI; budget the full Day 19
   block.

4. **Used `torch.save` for checkpoints.** Doesn't shard, doesn't
   survive topology changes, can't recover from torn writes
   (`STEP_BY_STEP.md` item 4). *Costs:* Dimensions 1, 2.
   *Fix:* use `torch.distributed.checkpoint` (§6.3) with a manifest;
   reuse FSDP's `load_sharded_state_dict`.

5. **Wrote a beautiful framework no one wants.** Skipped stakeholder
   interviews; built for a hypothetical user; migration plan won't
   survive contact with real teams (`STEP_BY_STEP.md` item 5).
   *Costs:* Dimension 4 (the entire 15 points), Dimension 5 (cost
   model assumptions are unfounded). *Fix:* execute the Week 1 Day 1
   discovery block; commit `docs/stakeholder-interviews.md` before
   touching code.

6. **No cost model.** Lost the org-level argument because a "10×
   faster training" claim isn't credible without a $/job comparison
   (`STEP_BY_STEP.md` item 6). *Costs:* Dimension 5. *Fix:* ship the
   cost model with assumptions separated from outputs, and have a
   non-engineer read the executive summary.

7. **Tech talk read off slides for 40 minutes.** Bored everyone. A
   principal-level talk has a *story* (we had this problem; here's
   what we tried; here's what worked; here's what surprised us)
   (`STEP_BY_STEP.md` item 7). *Costs:* Dimension 6. *Fix:* record,
   watch, re-record once; lead with the hardest decision, not the
   architecture diagram.

8. **ADRs that say "we chose X because X is best."** No alternatives,
   no consequences — reads as junior
   (`STEP_BY_STEP.md` item 8). *Costs:* Dimension 3 cap at Level 2.
   *Fix:* enforce a template (context, decision, alternatives
   considered, consequences accepted, status, date, deciders);
   require the alternatives section to name at least two.

9. **Multi-cluster claimed in the doc, not in the code.** A reviewer
   will spot it in 30 seconds (`STEP_BY_STEP.md` item 9). *Costs:*
   Dimensions 1, 3 (credibility). *Fix:* either build the second
   backend or scope it out explicitly in the design doc.

10. **Cost model has no assumptions section.** Numbers without
    assumptions are not credible (`STEP_BY_STEP.md` item 10).
    *Costs:* Dimension 5. *Fix:* a separate, named "Assumptions"
    sheet/section; mark each assumption as conservative / base /
    aggressive.

11. **Confused "senior staff agree" with "senior staff are bought
    in."** Agreement is intellectual; buy-in is emotional. You need
    both (track-level `mod-501/SOLUTION.md` §"Stakeholder mapping").
    *Costs:* Dimension 4. *Fix:* the migration plan must include
    named adopters and their explicit success criteria, not just an
    interview transcript.

12. **Bound the on-call surface late.** "Who is on call when a
    framework job stalls at 3am?" is `architecture.md` §12 open
    question 6 — leaving it for after launch is the single biggest
    operational regret graders see. *Costs:* Dimensions 2, 4.
    *Fix:* answer it in the design doc; the framework team owns
    framework-layer pages, user teams own model-layer pages, and the
    boundary is documented in `docs/oncall.md`.

13. **Hand-waved the simulation strategy** when hardware was
    constrained. The project README is explicit: at principal level,
    hand-waving is not acceptable
    (`README.md` §"Infrastructure assumed available"). *Costs:*
    Dimension 1 credibility. *Fix:* describe the simulation
    explicitly — e.g., 256-rank emulation on 8 GPUs with delay
    injection on NCCL collectives — and document the math that says
    the simulation is faithful.

14. **No "what I'd do differently" content anywhere.** The rubric
    awards points for it; graders flag its absence
    (`rubric.md` §"Calibration Notes for Reviewers"). *Costs:*
    Dimensions 3, 6. *Fix:* one ADR explicitly captures a decision
    you would reconsider; one slide in the talk does the same.

15. **Treated the framework as a closed shop on observability.**
    The metric/log schema is a public API
    (`architecture.md` §7). Changing it later breaks every adopter's
    dashboards. *Costs:* Dimensions 2, 4. *Fix:* version the schema;
    treat changes as semver-major.

---

## 6. References

### 6.1 PyTorch FSDP (official)

- PyTorch documentation, `torch.distributed.fsdp`: <https://pytorch.org/docs/stable/fsdp.html>
- PyTorch FSDP API reference (`FullyShardedDataParallel`, `BackwardPrefetch`, sharding strategies): <https://pytorch.org/docs/stable/fsdp.html>
- "FSDP" announcement and design overview on the PyTorch blog: <https://pytorch.org/blog/introducing-pytorch-fully-sharded-data-parallel-api/>

### 6.2 DeepSpeed ZeRO (official)

- DeepSpeed documentation, ZeRO: <https://www.deepspeed.ai/tutorials/zero/>
- ZeRO-Infinity and CPU/NVMe offload reference: <https://www.deepspeed.ai/tutorials/zero-offload/>

### 6.3 PyTorch Distributed Checkpoint (DCP) (official)

- `torch.distributed.checkpoint` reference: <https://pytorch.org/docs/stable/distributed.checkpoint.html>
- Sharded state-dict APIs on `FullyShardedDataParallel` (`load_sharded_state_dict`, `save_sharded_state_dict`): same `fsdp.html` reference as §6.1.

### 6.4 Kubeflow Training Operator and `PyTorchJob` CRD (official)

- Kubeflow Training Operator documentation: <https://www.kubeflow.org/docs/components/training/>
- `PyTorchJob` CRD reference: <https://www.kubeflow.org/docs/components/training/pytorch/>

### 6.5 PyTorch Distributed (official)

- `torch.distributed` overview: <https://pytorch.org/docs/stable/distributed.html>
- Backend selection (NCCL, Gloo, MPI) guidance: <https://pytorch.org/docs/stable/distributed.html#backends>

### 6.6 NCCL (NVIDIA, official)

- NCCL user guide: <https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/index.html>
- NCCL environment variables (including `NCCL_DEBUG`, transport selection): <https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html>

### 6.7 NVIDIA DCGM (official)

- DCGM documentation: <https://docs.nvidia.com/datacenter/dcgm/latest/>
- DCGM exporter for Prometheus: <https://github.com/NVIDIA/dcgm-exporter>

### 6.8 Cloud workload identity (official)

- AWS IAM Roles for Service Accounts (IRSA): <https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html>
- GKE Workload Identity: <https://cloud.google.com/kubernetes-engine/docs/concepts/workload-identity>
- AKS Workload Identity: <https://learn.microsoft.com/azure/aks/workload-identity-overview>

### 6.9 Kubernetes security and operator patterns (official)

- Pod security context and non-root containers: <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>
- Custom resources and operators: <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/>
- `kubectl debug` ephemeral debug containers: <https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/>

### 6.10 Megatron-LM (NVIDIA, reference architecture)

- Megatron-LM repository (tensor and pipeline parallelism reference): <https://github.com/NVIDIA/Megatron-LM>

### 6.11 Slurm (official)

- Slurm workload manager documentation: <https://slurm.schedmd.com/documentation.html>
- `sbatch` reference: <https://slurm.schedmd.com/sbatch.html>

### 6.12 Local exercise context

The following paths in the paired learning repository
(`ai-infra-principal-engineer-learning`) define the project the
solution defends. They are the authoritative source for every
project-specific requirement, metric, and acceptance criterion
referenced above:

- `projects/project-01-distributed-training/README.md` — project framing and outcomes
- `projects/project-01-distributed-training/requirements.md` — MoSCoW functional and non-functional requirements
- `projects/project-01-distributed-training/architecture.md` — target architecture, decision menu, failure model
- `projects/project-01-distributed-training/STEP_BY_STEP.md` — 100-hour build plan and validation gates
- `projects/project-01-distributed-training/rubric.md` — six-dimension scoring rubric
- `projects/project-01-distributed-training/deliverables/README.md` — 18-artifact submission inventory

The scenarios in `README.md` ("three teams independently re-discover
the same NCCL bug", "two teams burned a combined $1.4M") are
**learning-project framing**, not claims about a real incident.
Treat them as the principal-track's worked exercise context — the
same way a strategy case-study exercise uses a hypothetical company.

### 6.13 Track cross-references

- `ai-infra-principal-engineer-solutions/SOLUTION_OVERVIEW.md` — design philosophy for the principal-engineer track (judgment over implementation).
- `ai-infra-principal-engineer-solutions/modules/mod-501-technical-strategy/SOLUTION.md` — strategic-deliverable framing reused in the design-doc artifact.
- `ai-infra-principal-engineer-solutions/modules/mod-503-cross-org-initiative/SOLUTION.md` — stakeholder mechanics reused in the migration plan.
- `ai-infra-senior-engineer-solutions/` — implementation depth for a comparable framework lives here, not in this repo.
- `ai-infra-engineer-solutions/` — production-engineering reference for the per-component implementations.
