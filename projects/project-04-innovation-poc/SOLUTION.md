# SOLUTION — Project 04: Technical Innovation POC

> Read this *after* you have attempted the project in
> `ai-infra-principal-engineer-learning/projects/project-04-innovation-poc`.
> This is a **strategic deliverable**, not a runnable artifact. The
> "solution" is a worked decision frame plus rubrics — the
> principal-engineer job around an innovation POC is mostly
> *deciding what the POC should and should not do*, and *deciding
> what happens after it ends*. The code is the cheap part.

## 1. Solution overview

A technical innovation POC at principal-engineer scope is not a
prototype. It is a **time-boxed, falsifiable experiment** whose
purpose is to retire the largest unknown standing between the
organization and a candidate technical bet. The principal
engineer's value is concentrated at four moments:

1. **Framing** — picking *which* unknown to retire first, and
   stating it precisely enough that the POC's outcome is
   unambiguous.
2. **Scoping** — pulling out everything that is not on the
   critical path to retiring that unknown. POCs that try to also
   prove integration, scale, UX, and team velocity all at once
   prove none of them.
3. **Termination criteria** — declaring, *before* the POC begins,
   the observation that would graduate the POC into a real
   project, the observation that would kill it, and the deadline
   past which "inconclusive" counts as a kill.
4. **Disposition** — at the timebox, deciding among *graduate*,
   *extend with a new question*, *kill*, or *shelve*. The default
   must not be "let it drift."

The output contract for this project's strategic deliverables is
four short documents:

| Deliverable | Audience | Length |
|---|---|---|
| **POC charter** | sponsor, executing team | 1–2 pages |
| **Scope ladder** | executing team | 1 page |
| **Disposition memo** | sponsor, exec sponsor, adjacent teams | 1–2 pages |
| **Public retro** | broader engineering org | 1 page |

This module's "solution" is a worked example of each, the
decision rationale, the grading rubric, and the failure modes the
grader expects to see most often.

## 2. Worked answer or implementation

### 2.1 The POC charter

A POC charter has six fields and resists the temptation to add a
seventh. Worked example (illustrative; not a real product):

> **POC name:** Streaming-inference autoscaler — overcommit pilot
>
> **The unknown we are retiring:** Whether GPU memory
> overcommit + cooperative eviction can hold p99 token latency
> under a target SLO on a representative mixed-traffic shape,
> on a single accelerator family, in an isolated environment.
>
> **Why this is the load-bearing unknown:** Every downstream
> design choice (routing, billing, capacity planning) depends
> on the answer; without it, the larger bet ("multi-tenant
> serving on shared accelerators") cannot be sized.
>
> **In scope:**
> - Synthetic traffic generator matching the shape of one
>   sampled production hour.
> - Two-stage overcommit policy with a fixed cooperative
>   eviction hook.
> - Latency, throughput, and OOM-rate measurement on a single
>   accelerator SKU.
>
> **Out of scope:**
> - Authentication, billing, multi-region, multi-SKU,
>   production traffic, user-facing UX, integration with the
>   existing scheduler, hardening of the eviction hook.
>
> **Graduate criterion:** p99 token latency ≤ baseline + 15 %
> at ≥ 1.4× equivalent dedicated-accelerator throughput,
> sustained for ≥ 30 minutes on the synthetic shape, with
> OOM rate ≤ 0.1 % of requests.
>
> **Kill criterion:** Either (a) p99 latency exceeds baseline by
> 40 % at any sustained throughput, or (b) OOM rate exceeds
> 2 %, or (c) we cannot reach 1.0× equivalent throughput at
> all. Any one of these fires the kill.
>
> **Timebox:** 6 calendar weeks from kickoff. At week 6, if
> neither graduate nor kill has fired, the disposition is
> "inconclusive" and the POC is shelved with a written
> follow-up question, *not* extended in place.

Two things to notice. First, the unknown is stated in a way
that an observer can verify — "the answer is yes" or "the answer
is no" — rather than "we want to learn about overcommit."
Second, the kill criterion is *quantified and disjunctive* —
any of three measurements fires it, so the POC team cannot
talk itself out of a kill by emphasizing the metric that looks
best.

The numbers in the worked example are illustrative. Real POC
charters pick thresholds against a documented baseline and a
sponsor-approved SLO; the framework is what transfers, not the
numbers.

### 2.2 The scope ladder

Scope ladders fight a specific failure mode: the slow accretion
of "while we're in there" features that turn a 6-week POC into a
6-month half-product. Worked structure:

| Rung | Item | Decision |
|---|---|---|
| 0 (must have) | Synthetic traffic generator; overcommit policy; latency / throughput / OOM measurement | **In** |
| 1 (would be useful) | Recorded replay of real traffic instead of synthetic | **Out** — adds 2 weeks; synthetic is sufficient for the unknown |
| 2 (would be useful) | Second accelerator SKU comparison | **Out** — different question; new charter if pursued |
| 3 (looks small) | Integration with existing scheduler | **Out** — adds integration risk that confuses signal |
| 4 (looks small) | Web UI for results | **Out** — slide or notebook is enough; UI invites scope creep |
| 5 (tempting) | "While we're in there, refactor the eviction layer" | **Out** — would land in main branch; POCs do not own main-branch refactors |

The discipline: **everything above rung 0 is a "no" until the
graduate criterion has fired.** Items at rungs 1–5 become
candidate follow-up charters, not silent additions.

### 2.3 The disposition memo

At the timebox end, the POC owner writes the disposition memo
*before* the all-hands readout. The memo is short and follows a
fixed structure:

1. **What the POC asked.** (One paragraph; paraphrase the
   charter's unknown so a reader who hasn't seen the charter
   can follow.)
2. **What happened.** Measurements, with one chart per
   graduate / kill criterion. No prose interpretation in this
   section.
3. **The disposition.** One of `graduate`, `extend`, `kill`,
   `shelve`, with the criterion that fired.
4. **What we know now that we did not know at kickoff.**
   Two to four bullets. New unknowns surfaced go in section 5,
   not here.
5. **What we still don't know.** The follow-up questions, each
   sized into "next POC" or "absorb into the broader bet."
6. **What this implies for the bet.** Two paragraphs: the
   short-horizon implication (this quarter / next quarter) and
   the long-horizon implication (the bet itself — does this
   move the bet forward, sideways, or backward?).

The **single most important section is #3**: the disposition.
Memos that hedge the disposition ("the POC was promising and we
recommend continued investigation") are the failure mode that
produces zombie POCs. The disposition must be one of the four
named values, and the criterion that fired must be cited.

### 2.4 The public retro

A POC that runs and is never written up has produced 50 % of its
possible value. The other 50 % is the organization learning
something it can reuse. The public retro is a single page
covering:

- What we learned (the answer to the unknown).
- What surprised us (the unknowns we did not have at kickoff
  but discovered along the way).
- What the next decision is (who decides, by when).
- What the cost was (engineer-weeks, accelerator-hours, dollars
  spent — be honest).
- What we would do differently if we ran the POC again
  (process, not technical).

Public means: shared to the broader engineering org through the
normal channels (engineering all-hands deck, internal blog,
strategy review). Not "filed in a Confluence space no one
reads."

## 3. Validation steps

Because the deliverables are documents, the validation is
performed by review against the rubric in section 4. The
explicit validation steps a reviewer runs:

1. **Read the charter alone.** Without reading any other
   document, can the reviewer state in one sentence what
   observation would graduate the POC and what observation
   would kill it? If not, the charter has failed.
2. **Read the scope ladder alone.** Is every "out" decision
   justified by reference to either the unknown being retired or
   the timebox? "Out of scope" without a reason is a hidden
   "in scope" that will leak.
3. **Read the disposition memo alone.** Does the memo cite a
   specific criterion that fired? Is the disposition one of the
   four named values? Hedged dispositions fail this gate.
4. **Read the public retro alone.** A reader two desks away who
   knows nothing about the project must be able to summarize
   the result, the cost, and the next decision. If they cannot,
   the retro is technical-tribe-internal and has not done its
   force-multiplier job.
5. **Cross-reference the four documents.** Do the metrics in
   the disposition match the criteria in the charter? Do the
   "out of scope" items in the scope ladder show up as
   follow-up questions in the retro, or did they vanish?
6. **Time check.** Was the POC's wall-clock time inside the
   timebox? POCs that drift past their timebox are a
   process-failure signal even if the disposition was sound.

Reviewers should run these gates **in order** and stop at the
first failure. A charter that fails gate 1 cannot be saved by a
good disposition memo.

## 4. Rubric or review checklist

Score each item 0 / 1 / 2. Cut-line for "passes" is 16 / 24.

| # | Criterion | 0 | 1 | 2 |
|---|---|---|---|---|
| 1 | **Unknown is falsifiable** | Vague ("we want to learn about X") | Specific but ambiguous answer | One sentence; answer is yes/no/number |
| 2 | **Unknown is load-bearing** | Not connected to a larger bet | Connected but not load-bearing | Retiring it unlocks the next decision in a documented bet |
| 3 | **Scope ladder is exhaustive** | Missing or aspirational | Some "out" items, no rationale | Rung-by-rung, each "out" justified |
| 4 | **Graduate criterion is quantified** | Qualitative or missing | Quantified on one dimension only | Quantified on the dimensions a reasonable skeptic would attack |
| 5 | **Kill criterion is disjunctive and quantified** | Missing or only graduate stated | One kill condition | Two or more kill conditions, any-fires semantics |
| 6 | **Timebox is fixed and honored** | No timebox or slipped silently | Slipped with written justification | Inside timebox or explicitly killed at deadline |
| 7 | **Disposition is one of four named values** | Hedged narrative | Disposition stated but criterion not cited | Disposition + cited criterion that fired |
| 8 | **Public retro reaches outside the POC team** | Filed, unread | Shared, low engagement | Cited in subsequent strategy work |
| 9 | **Cost is reported honestly** | Not reported | Reported in engineer-weeks only | Reported in engineer-weeks + dollars + opportunity cost |
| 10 | **Follow-up unknowns are sized** | Listed only | Listed and prioritized | Listed, prioritized, and turned into candidate charters |
| 11 | **POC did not silently land in main** | Refactors landed under POC banner | Some artifacts landed but reviewed | POC code stayed isolated; only intentional, reviewed artifacts merged |
| 12 | **Conflict of interest acknowledged** | Author argues for own continued employment on the POC | Mentioned in passing | Explicit; author is willing to recommend kill on their own work |

### Reviewer guidance

- Item 4 and item 5 together are the single highest-signal
  pair. A POC with quantified graduate but soft kill criteria
  has been pre-rigged to graduate. Score these strictly.
- Item 12 is the principal-engineer-specific item. The POC
  author often becomes the candidate tech lead of the
  graduated project; the rubric forces acknowledgment of the
  resulting incentive bias.
- A score below 16 should not produce a "fix the document"
  outcome; it should produce a "re-frame the POC" outcome.
  Documents are downstream of framing.

## 5. Common mistakes

The mistakes below are the ones graders see most often.
Numbered for ease of citation in review comments.

1. **The POC has no falsifiable question.** "Build a POC of X"
   is not a falsifiable question; "Determine whether X can hold
   SLO Y on workload Z within K weeks" is. POCs without a
   question retire no risk and tend to drift into early-stage
   product builds.

2. **Soft kill criteria.** "If it doesn't look promising we'll
   stop" is not a kill criterion. It is a wish. Real kill
   criteria are numeric, observable, and disjunctive (any of
   N fires the kill), and they are written down *before* the
   first commit.

3. **Graduate criteria that are easier to hit than the bet
   demands.** A POC that proves a system can sustain a
   workload 0.1× the size of the real bet has not retired the
   load-bearing unknown. Graduate criteria must be calibrated
   against what the bet actually needs, not what is easy to
   demonstrate.

4. **Scope creep dressed as "while we're in there."** The
   second most common mode of POC failure. Each new addition is
   individually plausible; cumulatively they turn a 6-week
   experiment into a permanent half-product. The scope ladder
   exists to make these additions visible.

5. **Hedged dispositions.** "The POC was promising and we
   recommend continued investigation" is a non-disposition. It
   produces zombie POCs that consume budget for years without
   ever graduating or being killed. The disposition memo must
   pick one of four named values.

6. **POC code silently landing in main.** Hot-take refactors,
   "small" library upgrades, and helper utilities all want to
   piggyback on the POC. Each one ties the main branch to the
   POC's outcome and makes kill expensive. Keep POC code in a
   throwaway branch or directory; merge only what the
   disposition memo explicitly graduates.

7. **No public retro.** The POC team learned something; the
   organization did not. The next time the question comes up,
   someone re-runs a similar POC because there's no findable
   record of the first one. The retro is a small document with
   outsized leverage.

8. **The principal engineer who pitched the POC also writes
   the disposition memo without disclosure.** Predictable
   outcome: the memo recommends graduate. The conflict of
   interest is intrinsic; the cure is acknowledgment plus a
   reviewer who *is* empowered to overrule the author.

9. **Treating the timebox as advisory.** A 6-week POC that
   takes 14 weeks has produced a different artifact than the
   one that was funded. The timebox is part of the experimental
   design; missing it changes the result.

10. **POCs run in series when they could run in parallel — or
    in parallel when they should run in series.** Two unknowns
    that are independent should be POC'd in parallel; two
    unknowns where the second's framing depends on the first's
    answer must be serialized. Getting this wrong wastes
    quarters.

11. **Conflating "POC" with "MVP."** A POC retires an unknown
    for the building team; an MVP tests a hypothesis with real
    users. The two have different audiences, different metrics,
    and different exit criteria. Charters that confuse them
    produce artifacts that succeed at neither role.

12. **Failure-to-shelve.** "Inconclusive" is a valid POC
    outcome and should map to *shelve with a follow-up
    question*, not to *extend in place*. Extended-in-place POCs
    accumulate sunk cost without accumulating information.

## 6. References

Because the project is a strategic deliverable, the references
are method references rather than implementation references.
Use the ones below first; do not invent specific case studies
or citations.

### Curriculum cross-references

- [`SOLUTION_OVERVIEW.md`](../../SOLUTION_OVERVIEW.md) — design
  philosophy across the principal-engineer track; the framing
  for why a "solution" here is documents, not code.
- [`modules/mod-501-technical-strategy/SOLUTION.md`](../../modules/mod-501-technical-strategy/SOLUTION.md)
  — the strategy frame that an innovation POC sits inside. A
  POC outside a larger strategy is usually a personal-curiosity
  project in a corporate wrapper.
- [`modules/mod-505-long-term-technical-bets/SOLUTION.md`](../../modules/mod-505-long-term-technical-bets/SOLUTION.md)
  — the long-bet framework that a POC's load-bearing unknown
  should map to. The graduate / kill criterion language in this
  document is a deliberate echo of the kill-criterion language
  in mod-505.
- [`modules/mod-503-cross-org-initiative/SOLUTION.md`](../../modules/mod-503-cross-org-initiative/SOLUTION.md)
  — the cross-org coalition work required to make a graduated
  POC actually become a project.

### Paired learning material

- [`ai-infra-principal-engineer-learning/projects/project-04-innovation-poc`](https://github.com/ai-infra-curriculum/ai-infra-principal-engineer-learning/tree/main/projects/project-04-innovation-poc)
  — attempt the project first; this document is the answer
  key, not the assignment.

### Method references (read for technique, not for facts to
cite)

- The "hypothesis → falsifiable test → disposition" structure
  is the standard pattern of experimental method; any
  introductory text on experimental design covers it.
- The distinction between POC, prototype, MVP, and pilot is
  documented across multiple product-engineering texts. The
  charter in section 2.1 is in the POC quadrant: internal
  audience, retire-the-unknown purpose, no real users.
- For the cross-track view of how technical bets are framed
  at the architectural layer, see the principal-architect
  curriculum's long-horizon investment module.

<!-- Optional future enhancement: a worked external example (a published post-mortem of an innovation-POC graduate/kill decision from a named engineering org) would strengthen the rubric, but the framework above is self-contained and does not depend on one. -->
