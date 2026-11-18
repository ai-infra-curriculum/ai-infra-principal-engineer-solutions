# Exercise 01 — Case Study: A Technical Strategy Decision

> Companion reference for
> `lessons/mod-501-technical-strategy/exercises/exercise-01-case-study.md`.
> Read the module-level [SOLUTION](../SOLUTION.md) first for framing on
> what separates a **principal-engineer** case study from a
> principal-architect one.

## 1. Solution overview

The deliverable is a 1,500-3,000 word case study on a real technical
strategy decision, framed through the principal-engineer lens. The
learner's job is not to describe *that* a choice was made but to
excavate the **engineering substance** of the choice: what the
implementers saw that the architects did not (or vice versa), what
the alternative implementation would have been, and what it felt like
at 3am.

"Correct" answers are ones that:

- Pick a case whose **engineering trade-offs** are documented
  publicly enough to cite primary sources.
- Produce a counter-engineering alternative that is at least as
  specific as the chosen path.
- Pass the "operational reality" test — the 3am view, not the
  all-hands-slide view.

This exercise is graded against a rubric (see section 4). It does
not produce runnable code.

## 2. Implementation — worked exemplar: Stripe's commitment to idempotency keys

This is a short, grounded sketch, not a full deliverable. A
submission-length case study would expand each point.

### Context and the decision

In its early API versions Stripe committed to **idempotency keys as
a first-class API-layer primitive**: clients supply a key with each
mutating request, and the server guarantees at-most-once semantics
for that key over a defined window. This is documented publicly by
Stripe engineering.[^stripe-idempotency]

The technical-strategy *choice* was not "should we handle retries"
(every payments company does). The choice was **where to put the
retry-safety boundary** — in the client, in a proxy, in a
transactional database, or at the public API layer.

### Engineering substance, not architectural shorthand

An architect's version of this case study might stop at "Stripe
chose at-most-once semantics." The principal-engineer version has
to go further:

- The *implementation* bet is that a shared idempotency store (keyed
  by request hash + client key) can be kept consistent under the
  same availability profile as the API itself — i.e., the store
  must not become a new single point of failure.
- Writing to that store on the *write path* changes every mutating
  endpoint's latency and failure-mode surface. This has to be
  acceptable on the hottest endpoints (`/v1/charges`).
- Replaying a prior response has to be **byte-identical** or you
  create a new class of silent client-visible drift. That is an
  engineering invariant, not an architectural one.

### The counter-engineering alternative

A defensible alternative was **client-side retry with
request-level deduplication at the database**: let the client send
unlimited retries, catch duplicates at the row level by unique
constraint on an application ID, and reject dupes.

This would have been simpler to ship but shifts the burden of
correctness to every client integration and every internal caller.
The implicit bet Stripe made was that **the integrator surface is
large and heterogeneous enough that correctness has to live on the
server side**. That is an engineer's judgment call.

### The 3am view

The relevant on-call reality for this choice is:

- When the idempotency store degrades, do mutating writes fail
  closed or open? (Fail closed — correct for a payments company,
  expensive on a bad day.)
- What does a hot-key incident look like? (A single integrator
  sending the same key at high QPS can create write contention on
  one store partition.)
- What is the rollback path if a bad deploy starts returning
  non-idempotent responses? (Freeze mutating endpoints, re-serve
  from the store; the primitive is the recovery mechanism, which is
  a desirable property.)

A good case study closes on **what the engineers learned** that
reshapes future strategy — e.g., "treat the primitive that makes
your SLA cheap as a first-class product, with its own SLO and its
own operational runbook."

[^stripe-idempotency]: Stripe, "Designing robust and predictable
APIs with idempotency," Stripe engineering blog.
<https://stripe.com/blog/idempotency>. See also the current API
reference on the `Idempotency-Key` header,
<https://docs.stripe.com/api/idempotent_requests>.

### Other acceptable case subjects

Any case where you can cite primary engineering material (post-mortem,
blog, conference talk, paper) works. The module-level
[SOLUTION](../SOLUTION.md) lists additional seeds (Twitter/MySQL,
"go microservices," custom orchestration in the pre-Kubernetes era).
Pick a case where the *implementation* detail is public.

## 3. Validation steps

Before submitting, the learner should check each item below. These
are mechanical checks a reviewer can run quickly:

1. **Primary source test** — every factual claim about the subject
   organization has a citation to that organization's own
   engineering material (blog, talk, paper, post-mortem,
   open-sourced code), not a secondary summary. If a claim cannot
   be cited, flag it for follow-up research and remove it from the
   argument (do not substitute speculation).
2. **Counter-engineering test** — section 2.3 (or equivalent) names
   at least one specific alternative implementation path, not a
   different architectural posture.
3. **3am test** — the case study contains at least one paragraph
   on operational reality: what breaks, how it's noticed, what the
   recovery is.
4. **Specificity test** — at least one number appears (latency,
   QPS, error rate, incident count, dollars, hours of toil). If no
   number appears, the author has not committed to the engineering
   substance.
5. **Length test** — 1,500-3,000 words. Shorter means the depth is
   missing; longer usually means the architectural shorthand has
   crept back in.

## 4. Rubric

Score each dimension 0-3; passing is ≥ 10 total with no dimension at
0. Written for graders.

| # | Dimension | 0 — missing | 1 — thin | 2 — solid | 3 — exceptional |
|---|-----------|-------------|----------|-----------|-----------------|
| 1 | Case selection | Not a technical strategy decision, or not researchable from primary sources. | A real case but the engineering substance is largely private/unknowable. | A case with publicly documented engineering detail. | A case whose engineering detail is public *and* whose outcome is now visible (worked / didn't work), enabling a retrospective. |
| 2 | Engineering substance | Stops at the architectural label. | Mentions one or two implementation details. | Implementation details are specific enough to replicate the decision. | Implementation details tie directly to measurable operational outcomes. |
| 3 | Counter-engineering | No alternative considered. | Alternative is an architectural shift rather than an implementation path. | A specific alternative implementation is described. | Alternative is described in enough detail that a reader could cost-estimate it. |
| 4 | Operational reality | No 3am view. | Hand-waves ops ("we'd monitor it"). | Names a specific failure mode and its recovery path. | Walks through at least one real incident the chosen strategy created or prevented, with citation. |
| 5 | Judgment transfer | No lesson for future decisions. | A generic lesson ("be careful about microservices"). | A lesson specific to this class of decision. | A lesson that updates how the reader would approach a different-but-related strategy choice. |

Pass thresholds:

- **Below 8** — return for rewrite; ask the learner to pick a case
  with more public substance.
- **8-9** — accept with revisions; note the weakest dimension.
- **10-12** — accept as-is.
- **13-15** — flag as curriculum exemplar.

## 5. Common mistakes

Drawn from the module-level [SOLUTION](../SOLUTION.md#common-mistakes-graders-see)
and sharpened for this exercise:

1. **Writing an architect's case study.** "They chose
   microservices" is a posture. "They split the ledger service at
   the account-boundary and accepted saga complexity on the
   payout flow" is a technical strategy. If the case study reads
   like a conference abstract, it's the former.
2. **No counter-engineering.** Every technical strategy has at
   least one credible alternative implementation. A case study
   that doesn't name one hasn't stress-tested the decision.
3. **Treating a public outcome as proof the decision was right.**
   Survivor bias. The question is whether the decision was good
   *given what was knowable at the time*, not whether the company
   eventually did well.
4. **Using the case to grind an axe.** "They should have used X"
   is not a case study; it's an opinion. The case study job is to
   reconstruct the engineers' option set and reasoning, not to
   score it against your preferred stack.
5. **Metrics-free reasoning.** A case study with no numbers has
   not touched the engineering reality. If the primary sources
   don't publish numbers, the case is probably not a good
   researchable candidate — pick another.
6. **Confusing technical strategy with technology selection.** See
   the module-level common mistakes; "we'll use Rust" is not a
   technical strategy.

## 6. References

Primary sources used in the worked exemplar:

- Stripe, "Designing robust and predictable APIs with
  idempotency," <https://stripe.com/blog/idempotency>.
- Stripe API Reference, "Idempotent requests,"
  <https://docs.stripe.com/api/idempotent_requests>.

Framework references that align with the principal-engineer view:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>. The
  "Map / Measure / Manage / Govern" functions are a useful lens for
  AI-infra strategy cases: a good case study usually traces how
  one of those functions was (or wasn't) exercised in the subject
  decision.
- MITRE ATLAS — <https://atlas.mitre.org/>. For AI-system case
  studies, ATLAS's tactic/technique taxonomy gives a shared
  vocabulary for describing what the strategy did or didn't
  defend against.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Analogue of the above for ML-specific failure modes; useful
  when the case concerns an ML-system strategy decision.

Local context:

- Module-level framing on what a principal-engineer case study
  must contain: [SOLUTION](../SOLUTION.md#case-study-exercise-01-the-technical-strategy-decision).
