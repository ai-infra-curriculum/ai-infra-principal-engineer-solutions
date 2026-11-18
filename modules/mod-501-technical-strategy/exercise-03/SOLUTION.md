# Exercise 03 — Stakeholder Mapping: The Engineering Coalition

> Companion reference for
> `lessons/mod-501-technical-strategy/exercises/exercise-03-stakeholder-mapping.md`.
> Read the module-level [SOLUTION](../SOLUTION.md) first — the
> principal-engineer coalition is **IC-heavy** by design, not by
> accident.

## 1. Solution overview

The deliverable is a stakeholder map and engagement plan for the
technical strategy from [exercise-02](../exercise-02/SOLUTION.md).
It is a **coalition-building artifact**, not a RACI matrix: the
goal is to name who must be bought in for the strategy to execute,
what their failure mode looks like if they aren't, and what you'll
do about it.

The module-level SOLUTION lists the four stakeholder classes that
matter for a principal-engineer coalition:

- Senior ICs on the affected teams.
- On-call rotation owners.
- Principal architects.
- Product engineering leadership.

A correct map:

- Names at least one specific person (or clearly-labeled role) per
  class, not a generic bucket.
- Distinguishes **agreement** (intellectual) from **buy-in**
  (emotional) for each stakeholder.
- Has an engagement sequence that explains *why* the order is what
  it is.

This exercise is graded against a rubric. It does not produce
runnable code.

## 2. Implementation — worked exemplar: mapping for the reconciliation-pipeline bet

Continuing the technical bet from
[exercise-02](../exercise-02/SOLUTION.md). Names are illustrative
role labels; real submissions should use real names and link to
people pages where appropriate.

### 2.1 The stakeholder map

| Class | Who (role) | What they care about | Current state | Needed state | If not bought in, failure mode |
|-------|------------|----------------------|---------------|--------------|-------------------------------|
| Senior IC | `ledger` tech lead | Correctness of the reservation protocol; not being left holding migration burden. | Agrees in theory; skeptical of timeline. | Co-author on the design doc. | Will quietly keep writing to the old path; migration stalls at 20%. |
| Senior IC | `payouts` tech lead | Payout latency SLA; failure modes during the shadow phase. | Neutral. | Review-approver on the shadow-mode plan. | Will escalate the first latency incident as a cutover blocker. |
| Senior IC | ML platform tech lead | Whether the event log can later double as a feature-pipeline source. | Interested. | Advisory — not on critical path. | Will push for scope creep. |
| On-call owner | `ledger-oncall` chair | Pager volume, 3am diagnostic difficulty. | Skeptical. Last pager storm was 6 weeks ago. | Owner of the runbook for the new path. | Will veto cutover during any pager-heavy week. |
| On-call owner | `payouts-oncall` chair | Clear handoff for cross-service incidents. | Neutral. | Review-approver on the incident-flow doc. | Cross-service incidents become finger-pointing. |
| Principal architect | Payments platform PA | Consistency with the broader event-sourcing posture. | Supportive but wants to see failure-mode analysis. | Co-signer on the memo. | Will raise architectural objections late. |
| Principal architect | Data platform PA | Whether the event log becomes a data-platform dependency. | Not consulted yet. | Consulted; aligned on ownership boundary. | Data platform picks up an unowned dependency. |
| Product eng leadership | Payments PM lead | Roadmap risk; whether this blocks Q3 feature work. | Nervous. | Convinced the kill criterion protects the roadmap. | Roadmap commitments get re-negotiated under pressure. |
| Product eng leadership | VPE, Payments | Headcount and budget. | Will defer to principal-engineer judgment *if* the coalition is visibly solid. | Backing the decision publicly. | Funding ambiguous; team re-prioritizes mid-flight. |
| Finance eng | Finance eng lead | Audit trail integrity across the migration. | Not consulted. | Sign-off on shadow-audit methodology. | Audit finds a gap after cutover. |

### 2.2 Agreement vs. buy-in

The core distinction from the module-level SOLUTION: **agreement
is intellectual, buy-in is emotional**. The engagement plan has to
treat them separately.

- Several stakeholders above (both PAs, the `payouts` tech lead,
  the ML platform lead) are candidates for **agreement without
  buy-in** — they will nod in the review but won't spend political
  capital for the strategy.
- The `ledger` tech lead and the `ledger-oncall` chair are
  candidates for **buy-in without agreement** — they'll back the
  coalition because they trust the author, even if they'd have
  made a different call. This is valuable but fragile; if the
  first incident goes badly, that support evaporates.
- The combination that actually ships a technical strategy is
  **agreement and buy-in** on the senior ICs who will execute,
  and **agreement** on the architects and product leadership.

### 2.3 Engagement sequence (and why)

1. **Pre-brief the `ledger` and `payouts` tech leads
   individually**, before the strategy memo is circulated. If the
   senior ICs who will execute disagree, the strategy isn't
   ready; fix the strategy or kill it. Do not socialize a memo
   the ICs haven't blessed.
2. **Pre-brief the on-call chairs**, with the failure-mode
   analysis already written. Their input should modify the memo,
   not be bolted on after.
3. **Co-review with the principal architects**. The payments PA
   is the architectural signer; the data platform PA is a
   dependency owner. Going to the architects third (not first)
   signals that the engineering judgment is already consolidated.
4. **Brief the VPE and the PM lead**. They should see a memo that
   already has IC and architectural alignment; their question is
   "does this fit the roadmap?", not "is this a good idea?".
5. **Finance engineering walkthrough** before the shadow phase
   starts, not before memo approval — but on the critical path
   for the cutover.

The sequence is **IC-first, architect-second, leadership-third**.
This is the principal-engineer pattern. The inverse
(leadership-first) is a principal-architect pattern and will
produce an unexecutable strategy because the senior ICs weren't
part of the shape.

### 2.4 Coalition health indicators

Signals the plan is working:

- Senior ICs are correcting the memo's technical details, not
  rewriting its framing.
- On-call chairs are offering runbook text.
- Principal architects are asking failure-mode questions, not
  posture questions.

Signals the plan is failing:

- Senior ICs are "deferring to the principal engineer on the
  technical call." Deference is not buy-in.
- On-call chairs ask to "revisit after the next incident cycle."
  That means they are reserving veto power.
- Product leadership asks for the memo in Gantt form.

## 3. Validation steps

1. **Named-person test.** Every row of the map has a real person
   or a clearly-labeled role (e.g., "current chair of
   `ledger-oncall`"). Generic buckets ("engineering") fail.
2. **Class-coverage test.** The map has at least one entry in
   each of the four classes from the module SOLUTION: senior ICs,
   on-call owners, principal architects, product eng leadership.
3. **Agreement/buy-in test.** For each row, the "needed state"
   column distinguishes what you need intellectually from what
   you need emotionally. If every row says "aligned," the author
   has not done the distinction.
4. **Failure-mode test.** Every row has a concrete failure mode
   if the stakeholder isn't bought in. "Project slows down" is
   not concrete; "quietly keeps writing to the old path" is.
5. **Sequence test.** The engagement sequence has an explanation
   of ordering. If any two steps could be swapped without comment,
   the author hasn't committed to the IC-first discipline.
6. **Counter-coalition test.** Imagine the person who'd try to
   kill this strategy. Who do they call first? If that person is
   on the map and labeled, good; if not, the map is incomplete.

## 4. Rubric

Score each dimension 0-3; passing is ≥ 10 with no dimension at 0.

| # | Dimension | 0 | 1 | 2 | 3 |
|---|-----------|---|---|---|---|
| 1 | Coverage | Misses a required class. | All classes present but one is a single generic entry. | All classes present with specific entries. | All classes present, plus at least one "non-obvious" stakeholder (finance eng, legal, DX, etc.) justified in the text. |
| 2 | Specificity | Generic buckets. | Named roles. | Named individuals + what they care about. | Named individuals + what they care about + *current* state from evidence (recent incident, recent 1:1). |
| 3 | Agreement vs. buy-in | Not distinguished. | Mentioned but not applied per row. | Applied per row. | Applied per row with at least one case flagged as "buy-in without agreement" or vice versa, and an explicit plan for the fragility. |
| 4 | Engagement sequence | Not present or alphabetical. | Present but unjustified. | Sequence with per-step justification. | Sequence that is IC-first and explains *why* IC-first for this strategy. |
| 5 | Failure modes | Generic ("not aligned"). | Concrete for some rows. | Concrete for all rows. | Concrete + at least one failure mode names an observable leading indicator. |

Pass thresholds:

- **< 8** — return for rewrite; most common weakness is dimension
  3 (agreement vs. buy-in).
- **8-9** — accept with revisions.
- **10-12** — accept.
- **13-15** — curriculum exemplar.

## 5. Common mistakes

1. **The RACI substitution.** Submitting a RACI chart instead of a
   stakeholder map. RACI tells you who does what on tasks; a
   stakeholder map tells you who has to want the strategy to
   succeed.
2. **Leadership-first sequencing.** Briefing the VPE or the PM
   lead before the senior ICs. The memo then arrives on the ICs'
   desks as a fait accompli; the coalition they produce will be
   grudging compliance, not technical judgment.
3. **Treating "senior staff agree" as "senior staff are bought
   in."** The module-level
   [SOLUTION](../SOLUTION.md#stakeholder-mapping-exercise-03-the-engineering-coalition)
   calls this out as a primary failure mode.
4. **Skipping the on-call.** The dashboards don't show what breaks
   at 3am. A map without on-call owners is a map of the design
   review, not the production reality.
5. **No counter-coalition.** Every real strategy has someone who
   opposes it. If no one is on your map in that role, either you
   haven't found them or your strategy is not meaningful enough
   to care about.
6. **Static map, no cadence.** A map that isn't revisited after
   the first review session is wrong by week 4. Note a review
   cadence (biweekly, after each milestone, etc.).

## 6. References

Framework references:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>. The
  "Govern" function explicitly addresses roles, responsibilities,
  and cross-functional engagement; a mature stakeholder map can
  be read as a working document for Govern.
- MITRE ATLAS — <https://atlas.mitre.org/>. For AI-system
  strategies, security engineering and the model-ownership team
  should appear on the map when the strategy changes the attack
  surface.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Analogue of the above; use when the strategy moves ML data,
  model artifacts, or inference paths.

Local context:

- Module-level framing on why the coalition is IC-heavy:
  [SOLUTION](../SOLUTION.md#stakeholder-mapping-exercise-03-the-engineering-coalition).
- Upstream technical bet this coalition exists to execute:
  [exercise-02 strategy memo](../exercise-02/SOLUTION.md).
- Downstream execution plan the coalition will backstop:
  [exercise-04 roadmap](../exercise-04/SOLUTION.md).
