# Exercise 02 — Strategy Memo: The Technical Bet

> Companion reference for
> `lessons/mod-501-technical-strategy/exercises/exercise-02-strategy-memo.md`.
> Read the module-level [SOLUTION](../SOLUTION.md) first for the
> principal-engineer stance on technical bets.

## 1. Solution overview

The deliverable is a **2-4 page strategy memo** committing to a
specific technical bet, written at the altitude where a reader can
decide whether to fund the work. The module-level SOLUTION defines
the five required elements:

1. The bet (concrete, not a slogan).
2. The hard problem in the middle.
3. The engineering risks and fallback.
4. Success criteria as **engineering metrics**.
5. The "we'll know by" date.

"Correct" memos are the ones that could be dropped on a
principal-engineer peer's desk and get a yes, a no, or a specific
technical objection within 30 minutes — never a "come back when
you've thought about it more."

This exercise is graded against a rubric and produces a document,
not code.

## 2. Implementation — worked exemplar: moving account-balance updates off the synchronous SQL write path

This is a shortened worked example to show what the shape looks
like. All numbers in the exemplar are illustrative for the learning
context; a real memo would ground them in production telemetry.

---

### Memo: Reconciliation-pipeline migration for account balances

**Author:** <principal-engineer, payments-infra>
**Audience:** platform engineering leadership, on-call leads for
`ledger` and `payouts`, principal architect, finance engineering.
**Decision requested:** Fund a 2-quarter migration of
account-balance updates from synchronous SQL transactions to an
event-sourced reconciliation pipeline, with a kill criterion at the
end of Q1.

#### 1. The bet

We will migrate account-balance mutations from the current path —
synchronous row updates inside the API transaction — to:

- Writes produce an **event** on a durable log (one topic per
  account partition).
- A **reconciliation consumer** materializes the current balance
  into the read-model store.
- The API returns on event-commit, not on balance-materialization.

The bet is that **event-commit + eventual materialization** gives
us the write throughput we need without breaking the
customer-visible invariants.

#### 2. The hard problem in the middle

The hard problem is **not** "stand up Kafka" or "run a consumer."
Those are plumbing. The hard problem is:

> Maintaining a per-account invariant (balance never goes negative
> except via an authorized overdraft) when the authoritative check
> moves off the write path.

Specifically:

- The overdraft check currently runs inside the same transaction
  as the balance update. Moving it out requires a **reservation
  protocol** on the write path (pre-authorize a decrement before
  emitting the event) with a compensating action when the
  reconciliation diverges.
- The reservation store becomes the new correctness bottleneck. If
  we don't size it well, we've just moved the single-writer
  problem.

This is the genuinely novel work for our team. Everything else is
engineering we have done before.

#### 3. Engineering risks and fallback

Risks (ordered by expected damage × probability):

- **R1. Reservation-store contention** on hot accounts. Mitigation:
  partition by `account_id`, shadow traffic for 2 weeks before
  cutover, publish p99 reservation latency as an SLI.
- **R2. Materialization lag** making user-visible balance stale.
  Mitigation: serve reads through the reservation store during
  the window; add a "pending delta" field in API responses.
- **R3. Event-log durability gap** vs. the current SQL guarantee.
  Mitigation: run the event log at the same replication factor as
  the primary DB, and require quorum-ack on produce.

**Kill criterion:** if at the end of Q1 we cannot sustain **≥ 2×
current peak write QPS** in shadow with p99 reservation latency
**< 15 ms** and zero invariant violations in the shadow audit
log, we revert the branch, archive the design, and keep the
synchronous path.

#### 4. Success criteria (engineering metrics)

At steady state on live traffic:

- Write QPS headroom: ≥ 3× current peak without CPU-bound
  database nodes (today: ~1.3×).
- API p99 latency on mutating endpoints: ≤ current p99 + 5 ms
  (confirm current p99 baseline from live telemetry before
  publishing the memo).
- Invariant-violation rate from the shadow auditor: 0 per quarter.
- On-call pager volume on `ledger-oncall`: ≤ current, measured
  over 2 full rotations.

Non-criteria (deliberately excluded):

- Latency *improvement*. This bet is about headroom, not speed;
  committing to a latency win would distort the design.

#### 5. We'll know by

- **End of month 2:** shadow pipeline running against production
  traffic; reservation-store benchmarks published.
- **End of month 3 (= end of Q1):** kill-criterion checkpoint.
- **End of month 6:** 10% of mutating traffic on live path, 90%
  shadowed. Go/no-go for full cutover.

---

The exemplar is deliberately missing a few things a full
submission would include: a migration/rollback section
(see [exercise-04](../exercise-04/)), a stakeholder plan
(see [exercise-03](../exercise-03/)), and an engineering review
deck (see [exercise-05](../exercise-05/)).

## 3. Validation steps

A memo should pass all of these before it's shared:

1. **The one-line test.** Can you write the bet in one sentence
   that a peer could repeat accurately 24 hours later? If not,
   section 1 is still a slogan.
2. **The hard-problem test.** Section 2 names a problem that is
   novel *for this team*, not just novel in the abstract. If
   every line in section 2 could have been written before you
   started investigating, the hard problem isn't there yet.
3. **The number test.** Section 4 contains at least three numeric
   success criteria (not three criteria that happen to mention
   numbers — three criteria where the pass/fail judgment depends
   on a measured number).
4. **The fallback test.** Section 3 describes a kill criterion
   that a stranger could apply without consulting the author.
   "We'll reassess" is not a kill criterion.
5. **The date test.** Section 5 has **absolute** dates or absolute
   milestones from a fixed start, not "6 months in."
6. **The counter-memo test.** Draft the two-paragraph version of
   the *opposing* memo: "here's why we should not take this bet."
   If writing the opposing memo surfaces something section 3
   doesn't acknowledge, go back and fix section 3.

## 4. Rubric

Score each dimension 0-3; passing is ≥ 10 with no dimension at 0.

| # | Dimension | 0 | 1 | 2 | 3 |
|---|-----------|---|---|---|---|
| 1 | Specificity of the bet | Vague posture ("move toward event-driven"). | Named system but not the specific change. | Specific endpoint-level change. | Specific change with named invariants preserved. |
| 2 | Hard problem | Not named. | Named but boilerplate. | Specific and novel for the team. | Specific, novel, and the memo shows the author has already started attacking it. |
| 3 | Risk/fallback | Risks without mitigations. | Mitigations without a kill criterion. | Kill criterion present, mechanically checkable. | Kill criterion + budgeted fallback path (cost of reverting is known). |
| 4 | Metrics | Qualitative only. | Mix of qualitative and numeric. | All success criteria have numeric thresholds. | Numeric thresholds **and** an explicit non-criterion list. |
| 5 | Decision support | Could not get a yes/no in 30 min. | Could get a yes/no but not a defensible one. | A reviewer could defend the yes/no in a peer review. | A reviewer could defend the decision to a skeptical principal architect. |

Pass thresholds:

- **< 8** — return for rewrite.
- **8-9** — accept with revisions; typical weakness is dimension 3
  (fallback) or dimension 4 (numeric metrics).
- **10-12** — accept.
- **13-15** — curriculum exemplar.

## 5. Common mistakes

1. **The slogan bet.** "We'll become event-driven." The memo
   commits the organization to *something* but not to anything
   specific enough to kill.
2. **Vague success criteria.** "Improve scalability." A memo whose
   pass/fail you cannot adjudicate with a dashboard is not a
   strategy; it's a mood. See module-level
   [SOLUTION](../SOLUTION.md#strategy-memo-exercise-02-the-technical-bet).
3. **No hard problem.** The memo lists five "challenges," each of
   which the team has solved before. If none of them are novel,
   the strategy is probably a project, not a strategy — reframe.
4. **No fallback.** Every bet has a failure branch. If the memo
   doesn't name one, the team will escalate commitment past the
   point of good judgment.
5. **Stacking the metrics.** Choosing success criteria the team
   already hits. The number test above (section 3.3) catches this
   if the reviewer compares the thresholds to current telemetry.
6. **Dates relative to funding.** "In 6 months from kickoff" is
   not a commitment. Use absolute dates or absolute milestones
   from a declared start date.
7. **Confusing a memo with a design doc.** The memo is the
   decision artifact. Design detail belongs in a linked design
   document.

## 6. References

Framework references for grounding the memo:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>. The
  "Measure" and "Manage" functions correspond directly to section
  4 (success criteria) and section 3 (risks/fallback) of the
  memo; a reviewer may ask which NIST functions each section
  exercises.
- MITRE ATLAS — <https://atlas.mitre.org/>. For AI-system
  strategy bets, if the bet changes the attack surface (e.g.,
  model-serving boundary, training-data pipeline), section 3
  should reference the relevant ATLAS techniques.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Use when the bet is an ML-infra change (e.g., ML06 output
  integrity when moving to batch inference).

Local context:

- Module-level framing on the five required memo elements:
  [SOLUTION](../SOLUTION.md#strategy-memo-exercise-02-the-technical-bet).
- Downstream artifacts that depend on this memo:
  [exercise-03 stakeholder map](../exercise-03/SOLUTION.md),
  [exercise-04 roadmap](../exercise-04/SOLUTION.md),
  [exercise-05 engineering review](../exercise-05/SOLUTION.md).
