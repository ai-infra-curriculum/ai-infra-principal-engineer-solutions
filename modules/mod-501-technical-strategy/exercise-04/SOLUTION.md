# Exercise 04 — Roadmap: The Engineering Execution Plan

> Companion reference for
> `lessons/mod-501-technical-strategy/exercises/exercise-04-roadmap.md`.
> Read the module-level [SOLUTION](../SOLUTION.md) first for the
> four non-negotiables of a principal-engineer roadmap.

## 1. Solution overview

The deliverable is an **execution roadmap** for the technical bet
from [exercise-02](../exercise-02/SOLUTION.md), with four
non-negotiable elements (see module-level
[SOLUTION](../SOLUTION.md#roadmap-exercise-04-the-engineering-execution-plan)):

1. Concrete engineering milestones with measurable outputs.
2. Decision gates between milestones.
3. "This is where the technique gets hard" markers.
4. Production readiness criteria.

Plus a fifth, cross-cutting item that the module calls out as
mandatory: a **kill criterion**. Every real strategy has at least
one point where the right decision is to stop.

A correct roadmap passes the "what does this look like at 3am"
test *at every milestone*, not just at the end.

This exercise is graded against a rubric and produces a document
(markdown table + commentary), not code.

## 2. Implementation — worked exemplar: roadmap for the reconciliation-pipeline bet

Continues the technical bet from
[exercise-02](../exercise-02/SOLUTION.md) and the coalition from
[exercise-03](../exercise-03/SOLUTION.md). Dates are relative to a
declared start (`T+0`). Numbers are illustrative for the exemplar;
a real roadmap would ground them in current telemetry.

### 2.1 Milestones and gates

| ID | Milestone | Measurable output | Decision gate (go/no-go) | Hard-problem marker |
|----|-----------|-------------------|---------------------------|---------------------|
| M1 | Reservation-store design + bench | p99 reservation latency on single partition under synthetic load at **2× current peak write QPS** ≤ **10 ms**. | If p99 > 15 ms at 2× peak: redesign; do not proceed to M2. | **★** — this is the novel bit. |
| M2 | Event log + consumer (shadow) | Shadow pipeline consumes **100%** of production write events for 14 consecutive days with zero reconciliation drift against the SQL source of truth. | Drift events > 0 at end of window: open a correctness RCA before M3. | |
| M3 | Reservation protocol end-to-end | Reservation + emit + materialize round-trip p99 **≤ current API p99 + 5 ms** at current peak. | Over budget: profile and optimize; no M4 start until budget met or memo amended. | **★** — reservation/compensating-action correctness is here. |
| M4 | Shadow audit (invariant check) | 30-day shadow audit: **0** invariant violations (balance never non-authorized-negative). | Any violation: kill-criterion review (see §2.3). | |
| M5 | 10% live traffic | 10% of mutating traffic on new path; **on-call pager volume ≤ current** across one full rotation; **customer-visible error rate ≤ current**. | Pager volume up: roll back to 0%, publish RCA, reconsider plan. | |
| M6 | 50% live traffic | 50% cutover sustained for one full rotation; all M5 criteria still true; **p99 within budget**. | Any criterion fails: hold at 50% and investigate; no M7 until green for 7 days. | |
| M7 | Primary-path cutover | 100% of writes on the new path; old path kept **hot-standby** for 30 days. | 30 days clean → decommission old path. | |

### 2.2 "This is where the technique gets hard" markers

The module-level SOLUTION calls out these markers as mandatory.
This roadmap names **two**:

- **M1 — reservation store under hot-key load.** The literature
  on multi-leader / multi-partition reservation protocols is
  mature, but hot-key behavior under production traffic
  distributions is the thing that will bite. Budget **2x** the
  time of a normal design-and-bench milestone.
- **M3 — compensating-action correctness.** Moving the overdraft
  check off the write path means a bad decision has to be undone
  after the fact. The compensating action has to be
  idempotent, bounded in time, and auditable. This is novel for
  the team; budget a **design review with an external reviewer**.

Marking the hard parts explicitly is what distinguishes a
principal-engineer roadmap from a GANTT chart.

### 2.3 Production readiness criteria

These are preconditions for M5 (first live traffic), M6 (50%), and
M7 (primary path):

- **Runbook** for the reservation-store incident classes, authored
  by the `ledger-oncall` chair (see
  [exercise-03](../exercise-03/SOLUTION.md)).
- **Dashboards** for: reservation-store p99, event-log produce
  ack latency, materialization lag, shadow-audit-violation count.
  Each has an SLO and a pager threshold.
- **Chaos drill** completed: one full-region reservation-store
  outage exercised in shadow; results published.
- **Rollback drill** completed: cutover from 10% → 0% tested in
  staging; full rollback is one config change with a known blast
  radius.
- **Finance-engineering sign-off** on the shadow-audit
  methodology.

### 2.4 Kill criterion

Codified explicitly, callable at any time by any member of the
coalition:

> Stop the migration and preserve the current path if, at the end
> of M1, the reservation store cannot sustain **2× current peak
> write QPS** at p99 ≤ 15 ms **without horizontal partition
> explosion**; or if M4 produces any invariant violation; or if
> the `ledger-oncall` chair calls a pager-volume veto during M5
> or M6.

This clause is deliberately tight. Vague kill criteria
(`"we'll reassess"`) escalate commitment past the point of good
judgment. See
[SOLUTION](../SOLUTION.md#roadmap-exercise-04-the-engineering-execution-plan).

### 2.5 Timeline overview

| Phase | Window (relative) | Milestones | Coalition rhythm |
|-------|-------------------|------------|-------------------|
| Design + bench | T+0 → T+6 weeks | M1 | Weekly IC review; biweekly PA review. |
| Shadow | T+6 → T+14 weeks | M2, M3, M4 | Weekly on-call review; dashboard SLOs committed by end of this phase. |
| Partial live | T+14 → T+22 weeks | M5, M6 | Daily operational standup for first two rotations; rotate on-call chair into standup. |
| Cutover + bake | T+22 → T+30 weeks | M7 | Biweekly review; monthly post-mortem of near-misses even if no incidents. |

Calibrate T+0 to T+30 against current team velocity on comparable
pipeline-migration work before publishing externally.

## 3. Validation steps

1. **Milestone-output test.** Each milestone row has a
   **measurable output**. "Design complete" fails; "prototype
   handling 10k QPS in shadow with p99 < 100 ms" passes.
2. **Gate test.** Each row has an explicit go/no-go rule. If a
   milestone ends in a review meeting with no written criterion,
   it's not a gate — it's a status update.
3. **Hard-problem marker test.** At least one milestone is
   marked as "this is where the technique gets hard" with a time
   or review budget attached.
4. **Production readiness test.** Section 2.3 (or equivalent)
   exists and names runbook, dashboards, chaos drill, rollback
   drill, and the owner of each.
5. **Kill-criterion test.** Section 2.4 (or equivalent) is
   mechanically checkable — a reader without context could
   adjudicate it.
6. **Migration test.** The roadmap names when the old path turns
   off, not just when the new one turns on. The module-level
   SOLUTION flags this ("the new system exists; the old one
   still exists; nobody moved") as a top-grader complaint.
7. **3am test at every milestone.** Walk down the milestones and
   ask "what does a bad night look like at this milestone?" If
   you can't answer for at least M3 onward, the operational
   reality is still missing.

## 4. Rubric

Score each dimension 0-3; passing is ≥ 11 with no dimension at 0
(this exercise is weighted more strictly than earlier exercises
because the roadmap is the artifact the organization executes on).

| # | Dimension | 0 | 1 | 2 | 3 |
|---|-----------|---|---|---|---|
| 1 | Milestones | Not measurable. | Some measurable. | All measurable. | All measurable + at least one "what could make us stop" clause per milestone. |
| 2 | Gates | None or implicit. | Named but subjective. | Mechanical, could be adjudicated by a stranger. | Mechanical **and** each gate names the data source that answers it. |
| 3 | Hard-problem markers | Absent. | Present but not time-budgeted. | Present with time or review budget. | Present, time-budgeted, and references a specific reviewer/design-review commitment. |
| 4 | Production readiness | Absent. | Runbook only. | Runbook + dashboards + rollback. | Runbook + dashboards + rollback + chaos drill + owned sign-offs. |
| 5 | Kill criterion | Absent. | Vague ("reassess"). | Mechanically checkable. | Mechanically checkable, invocable by named coalition members. |
| 6 | Migration | Old path never turns off. | Decommission date present but unbacked. | Decommission date with hot-standby window. | Decommission date + hot-standby + rollback-from-decommission plan. |

Pass thresholds:

- **< 10** — return for rewrite.
- **10-13** — accept with revisions; most common weakness is
  dimension 6 (migration/decommission).
- **14-16** — accept.
- **17-18** — curriculum exemplar.

## 5. Common mistakes

1. **All design, no integration.** A roadmap that spends 80% of
   its wall-clock in design and prototyping and 20% in
   integration produces a system that doesn't fit production.
   See
   [SOLUTION](../SOLUTION.md#roadmap-exercise-04-the-engineering-execution-plan).
2. **No "what could make us stop" clause.** Kill criterion
   missing or vague. The module-level SOLUTION flags this as a
   defining failure.
3. **Decommission as an afterthought.** The new system is built;
   the old one still handles 40% of traffic a year later. A
   roadmap that doesn't name a decommission date is a
   half-migration.
4. **Status-review gates.** "Monthly check-in" is not a decision
   gate; it's a calendar invite. The gate needs a rule.
5. **Hiding the hard problem under "design and prototype."** If
   the roadmap doesn't mark the novel engineering as novel, the
   team will treat it as plumbing and miss by months.
6. **No runbook ownership.** The runbook exists but doesn't have
   a named owner. The first incident finds this gap.
7. **Metrics that drift during the roadmap.** Success criteria
   restated between milestones ("we'll re-baseline p99 after
   M3") — fine if planned, dishonest if used to paper over a
   missed target.

## 6. References

Framework references:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>. The
  "Measure" and "Manage" functions map directly onto the
  decision-gate and kill-criterion machinery here; for AI-infra
  roadmaps, each gate can be framed as a NIST measurement.
- MITRE ATLAS — <https://atlas.mitre.org/>. For AI-system
  cutovers, the shadow phase should include a verification that
  the migration does not open new ATLAS techniques (new
  model-serving endpoint, new training-data pipeline, etc.).
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Analogue of ATLAS for ML-infra roadmaps.

Local context:

- Module-level framing on the four roadmap non-negotiables:
  [SOLUTION](../SOLUTION.md#roadmap-exercise-04-the-engineering-execution-plan).
- Upstream technical bet:
  [exercise-02 strategy memo](../exercise-02/SOLUTION.md).
- Coalition that executes the roadmap:
  [exercise-03 stakeholder map](../exercise-03/SOLUTION.md).
- Communication artifact that presents this roadmap:
  [exercise-05 engineering review](../exercise-05/SOLUTION.md).
