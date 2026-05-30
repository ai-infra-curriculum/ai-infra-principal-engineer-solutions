# SOLUTION — Platform Integration (Project 02)

> Read this *after* you have attempted the project deliverables.
> Like the module solutions in this track, the "solution" is a
> rubric and a worked judgment pattern, not a runnable reference
> implementation. The shape of platform-integration work at the
> principal-engineer level is *deciding what to integrate, where
> the seams go, and who owns each seam* — not writing the glue
> code, which lives in the engineer and senior-engineer repos.

<!-- Authoring note: this solution was written against the project-level work packet, which declared no per-exercise deliverables. If the paired learning repo `ai-infra-principal-engineer-learning/projects/project-02-platform-integration` later enumerates sub-deliverables, split this document into per-exercise SOLUTION.md files following the same six-section contract. -->

## 1. Solution overview

A platform-integration project at the principal-engineer level is
a *boundary-design* problem first and an integration-engineering
problem second. The deliverable is a coherent decision about
**which capabilities live where**, **which contracts join them**,
**how teams move across the new line**, and **how the integrated
system fails safely**. The visible artifact is usually one or
more of:

- An integration RFC or platform interaction design.
- A contract specification (APIs, schemas, identity, telemetry).
- A migration / cutover plan with kill criteria.
- A stakeholder and ownership map (RACI or equivalent).
- A risk register with explicit mitigations.

The judgment under all of these: **a principal engineer is paid
to make the integration *not* be a permanent tax on every team
that touches it**. That requires choices that look conservative
in the short term — fewer integration points, simpler contracts,
clearer ownership — and pay off over years.

This solution covers the typical sub-deliverables a platform-
integration capstone tends to ask for. If the paired learning
repo declares a different exact list, map them against the
sections below — the underlying judgment is the same.

## 2. Worked answer / implementation

### 2.1 Boundary design — where does the seam go?

The first decision is *where the platforms touch*. Three patterns
recur, with very different consequences:

1. **Thin contract at the API boundary.** The two platforms
   exchange data and control only through a published API
   (synchronous or async). Each side owns its internals.
   *Use when*: the platforms have independent release cycles and
   neither side needs deep visibility into the other.
2. **Shared substrate.** Both platforms run on the same lower
   layer (identity, storage, scheduler, observability) and only
   the upper layers differ.
   *Use when*: the lower layer is already a stable platform of
   its own and dragging it into the integration would be a net
   regression.
3. **Embedded / library integration.** One platform is consumed
   as a library inside the other.
   *Use when*: the embedded platform is small, stable, and
   versioned conservatively. Rare at principal scale because
   library coupling cuts across team boundaries silently.

Principal-engineer judgment: prefer (1) by default. Move to (2)
only if the shared substrate is genuinely owned as a platform
(team, SLO, on-call). Avoid (3) unless the embedded thing has a
strong backward-compatibility contract.

### 2.2 Contract specification

A platform-integration contract has to specify, at minimum:

- **Surface** — request/response shape, async event schema,
  versioning policy (semver, additive-only, deprecation window).
- **Identity** — how the caller is authenticated, how authority
  is propagated (e.g., on-behalf-of), how machine identity
  rotates.
- **Authorization** — who can call what, expressed as policy
  the calling platform can evaluate without a network call to
  the called platform.
- **Telemetry** — trace context propagation, log correlation
  identifiers, an SLI set the integration owner publishes.
- **Failure semantics** — what does the called side do when
  it's degraded? What does the caller do when it sees that
  state? (Timeouts, retries, circuit-breaking, backpressure.)
- **Quota and isolation** — per-caller limits, noisy-neighbor
  protection, fair queueing.
- **Data contract** — schema evolution rules; PII / governance
  classification of every field; retention.

A common failure: the contract is written as an OpenAPI spec
without any of the last four bullets. The integration ships, and
the on-call rotations of *both* sides spend the next two quarters
inventing those rules under fire.

### 2.3 Identity and authorization across the seam

The integration is also a trust boundary. At principal scale
the question is not "do we authenticate?" but "what's the
identity model on each side, and how do they translate?"

The minimum a principal engineer should require:

- **Workload identity.** Services on each platform have a
  cryptographic identity that does not depend on shared secrets.
  (Cloud IAM, SPIFFE/SPIRE, mTLS with a managed CA, etc. —
  the specific choice matters less than that *one* is chosen
  and consistently enforced.)
- **End-user identity propagation.** If the integration handles
  user actions, the user identity must survive the hop — not be
  collapsed into the caller's machine identity. Otherwise audit
  trails lie about who did what.
- **Authorization at the seam, not behind it.** The called
  platform must enforce authorization itself; "we trust the
  caller to check first" creates a confused-deputy posture.

### 2.4 Telemetry and SLO design

The integrated path needs its own SLOs, not just the union of
the two sides' SLOs. A request that crosses the seam can fail
in ways that neither side sees as "their" failure:

- The caller times out before the callee returns success.
- The callee succeeds but the response is dropped on the wire.
- An async event is consumed but the downstream effect is
  silently dropped.
- A schema-evolution mismatch corrupts data invisibly until a
  downstream consumer fails.

A defensible SLO set covers: end-to-end success rate of the
integrated operation, end-to-end latency at the user-visible
percentile, schema-validation error rate, and data-loss rate
for async paths. Each SLO names an owner. (See the SRE book
for the canonical SLI/SLO framing.)

### 2.5 Migration / cutover plan

If the integration replaces an existing connection (and at
principal scale it usually does — green-field integrations are
rare), the migration plan is the load-bearing part of the
project. Two patterns work and one always fails:

- **Strangler fig** — new path is built behind the old one;
  traffic is moved fraction by fraction with the ability to
  reverse. (Fowler's pattern; widely documented.) Works.
- **Parallel run with reconciliation** — both paths run for a
  defined window, outputs are compared, divergences investigated
  before cutover. Works for data-plane integrations where
  silent corruption is the main risk.
- **Big-bang cutover with a feature flag** — looks like
  strangler fig but isn't, because nobody actually exercises
  the reverse path. Usually fails because the rollback isn't
  rehearsed.

Whichever pattern is chosen, the plan must name:
- **Kill criteria** — what observation makes us roll back?
- **Rollback rehearsal** — has the rollback path been exercised
  end-to-end against production-shaped data?
- **Migration owner** — single name, not "the team."
- **Migration end date** — including the date by which the old
  path will be *deleted*, not just disabled.

### 2.6 Ownership and stakeholder map

The integration creates a new artifact that needs an owner.
Common failure: the integration is built by one team and
operated by neither, drifting into shared-stewardship oblivion.

The principal-engineer call is one of:

- **Sole owner.** One existing team takes the integration into
  their on-call and roadmap. Cleanest, but the owning team
  takes a permanent tax and tends to resist.
- **New team.** Justified only if the integration is large
  enough to fund a team's existence indefinitely. (Almost
  never the case for a single integration.)
- **Joint stewardship with named primary.** Both sides are
  on-call, but one side is the primary point of contact and
  owns the roadmap. Requires explicit RACI and exec sign-off
  to survive a year.

The stakeholder map should additionally name: the on-call
rotations of both sides (their judgment trumps everyone else's
on operability), the product engineering teams whose roadmaps
depend on this integration, the security/identity owners, the
data governance owners, and the principal architect of the
broader platform stack.

### 2.7 Risk register

A defensible risk register at the principal-engineer level
states risks *with their mitigations and their owners*, not as
generic worries. The categories that matter for platform
integration:

- **Schema-evolution risk** — what happens when one side adds
  a field the other doesn't yet handle? *Mitigation:*
  additive-only changes within a major version; deprecation
  window stated; consumer-driven contract tests.
- **Capacity / blast radius risk** — what happens if the
  caller's traffic spikes 10×? Does it take down the callee?
  *Mitigation:* per-caller quotas, load shedding, isolation
  pools.
- **Identity / auth incident risk** — what happens when a
  workload identity is compromised? *Mitigation:* short-lived
  credentials, rotation, audit log of all cross-platform calls.
- **Vendor / dependency risk** — what happens if the underlying
  platform (cloud, managed service) makes a breaking change?
  *Mitigation:* abstraction at the seam such that the dependency
  can be swapped within one quarter.
- **Org risk** — what happens if the owning team is
  reorganized? *Mitigation:* the ownership decision is on
  record with an executive sponsor; the integration documents
  are checked in, not in someone's head.

## 3. Validation steps

Because the deliverable is judgment, the validation is structured
review rather than test execution. The grader (or self-reviewer)
walks the artifact against these checks:

1. **Boundary clarity.** Can a new engineer on either side read
   the design and correctly answer "does this code go on side A
   or side B?" without asking?
2. **Contract completeness.** Does the contract specify all of
   surface, identity, authorization, telemetry, failure
   semantics, quotas, and data contract? Spec-only-no-failure-
   semantics is a fail.
3. **SLO definition.** Are there SLIs and SLOs for the
   *integrated path*, not just for each side independently? Is
   each SLO owned by a named role?
4. **Migration plan exercised.** If the plan claims rollback,
   has the rollback been rehearsed against production-shaped
   data? Paper rollback plans are a fail.
5. **Single ownership.** Does the artifact name a single owner
   (person or role), not "the platform team" or "to be
   determined"?
6. **Risk register with mitigations.** Are risks paired with
   mitigations and owners? Risks without mitigations are
   worries, not engineering risk management.
7. **Kill criteria stated.** Is there a documented condition
   under which the project is rolled back / stopped, and is
   that condition observable from the telemetry the plan
   commits to?
8. **Production at 3 a.m. test.** Has the design been walked
   through by the on-call rotations of both sides, with
   specific failure scenarios? (e.g., "callee returns 500 at
   3 a.m. — what does the caller's pager say, who does it
   page, and what does the runbook say to do?")

Each of these is a binary pass/fail with evidence; "we will
have an SLO" is not a pass, "we have agreed SLOs A, B, C
documented at /path" is.

## 4. Rubric / review checklist

Grade out of 10 across these dimensions. Total ≥ 32 / 40 passes;
< 24 needs a rewrite; in between is a revise-and-resubmit.

| Dimension | 0 — Missing | 5 — Adequate | 10 — Excellent |
|---|---|---|---|
| Boundary design | No explicit seam; "we'll integrate" is undefined. | Seam pattern named (API / substrate / library) with rationale. | Seam pattern justified against the two main alternatives, with explicit trade-offs. |
| Contract specification | Surface only. | Surface + identity + telemetry. | Surface, identity, authorization, telemetry, failure semantics, quotas, data contract — each owned. |
| Identity / authz | Implicit shared secret or "we trust the network." | Workload identity + end-user propagation. | Above + authorization enforced at the seam + audit trail design. |
| SLOs | No integrated-path SLOs. | One end-to-end SLO. | End-to-end success + latency + schema-error + data-loss SLOs, each owned. |
| Migration plan | Big-bang cutover with no rehearsal. | Strangler fig or parallel run pattern named. | Pattern + kill criteria + rehearsed rollback + deletion date for old path. |
| Ownership | "TBD" or "shared." | Single owner named. | Single owner + RACI + exec sponsorship recorded. |
| Risk register | List of worries. | Risks with mitigations. | Risks with mitigations, owners, and observable triggers. |
| Stakeholder coverage | Engineering only. | Includes on-call + security. | Includes on-call, security, data governance, product engineering, principal architect peer review. |

A grader who sees four 10s and four 0s should fail the
deliverable: platform-integration work is load-bearing on its
weakest section, not its strongest.

## 5. Common mistakes

1. **Boundary drawn for the *current* org chart.** The seam
   matches the team boundaries that happen to exist today. The
   integration looks clean for two quarters and then a reorg
   leaves the boundary running through the middle of a team.
   *Inverse Conway maneuver*: design the boundary that *should*
   exist, then ask the org to match it — when feasible. (See
   Skelton & Pais, *Team Topologies*, for the framing.)
2. **No deletion date for the old path.** The strangler fig
   only works if the old trunk actually dies. Without a
   deletion date — and a person responsible for hitting it —
   both paths run indefinitely and the integration becomes a
   permanent tax.
3. **SLOs that union the two sides.** "Side A has 99.9%, side
   B has 99.9%, integrated path therefore 99.8%." This is
   arithmetic, not engineering. The integrated path has its
   own failure modes (timeouts, schema mismatches, async
   loss) that don't appear on either side's dashboards.
4. **Identity collapse.** End-user identity gets replaced with
   the calling service's machine identity at the seam. Audit
   logs now lie. This breaks compliance and incident response
   in ways that are nearly invisible until the first audit.
5. **Authorization at the caller.** The caller checks "is this
   user allowed?" before making the call, and the called side
   trusts that. Any other caller that learns the URL can now
   bypass the check. Authorization must live at the seam.
6. **No quota / isolation.** Day-one traffic is small, so no
   one notices the absence. Six months later a runaway batch
   job from one caller takes the platform down for everyone.
7. **Contract changes through code review.** The API spec is
   "whatever the code says today." Consumers break silently on
   deploys. Real platform integrations require *consumer-driven
   contract tests* or equivalent, run in CI on both sides.
8. **Ownership by committee.** No single owner; the integration
   is "shared." In practice nobody owns operability, and the
   integration degrades over time without anyone being
   accountable.
9. **Skipping the on-call review.** The design is approved by
   architects and tech leads but never walked through by the
   on-call engineers who'll actually carry the pager. The
   3 a.m. failure modes are discovered in production.
10. **Treating the integration as a one-time project.** The
    project ships, the team disbands, and the integration ages
    badly. A platform integration is a *durable artifact* with
    an operating model, not a one-shot deliverable.

## 6. References

Public, durable references that ground the rubric above. Use
these instead of vendor blog posts where they overlap.

- Conway, M. E. (1968). *How Do Committees Invent?* —
  the original statement of Conway's Law, foundational for the
  boundary-design judgment in §2.1 and the §5.1 anti-pattern.
- Skelton, M., & Pais, M. (2019). *Team Topologies*. IT
  Revolution Press. — current framing for boundary design,
  platform-as-a-product, and the inverse Conway maneuver.
- Fowler, M. *StranglerFigApplication.* —
  https://martinfowler.com/bliki/StranglerFigApplication.html —
  canonical reference for the migration pattern in §2.5.
- Beyer, B., Jones, C., Petoff, J., & Murphy, N. R. (eds.)
  (2016). *Site Reliability Engineering: How Google Runs
  Production Systems*. O'Reilly. — SLI/SLO/error budget
  framing used in §2.4 and §4.
- Beyer, B., Murphy, N. R., Rensin, D. K., Kawahara, K., &
  Thorne, S. (eds.) (2018). *The Site Reliability Workbook*.
  O'Reilly. — practical SLO definition and review patterns.
- Newman, S. (2021). *Building Microservices*, 2nd ed.
  O'Reilly. — contract design, consumer-driven contracts,
  and integration patterns (esp. chapters on inter-process
  communication and deployment).
- The Twelve-Factor App. — https://12factor.net/ — useful as
  a baseline for the "what does each side own?" line of
  reasoning, even where it doesn't fully fit a platform-scale
  integration.
- IETF RFC 8446 (TLS 1.3) and RFC 6749 (OAuth 2.0
  Authorization Framework) — for the identity / auth
  references in §2.3. SPIFFE/SPIRE documentation
  (https://spiffe.io/) for workload identity specifically.
- ISO/IEC/IEEE 42010:2022 — architecture description standard;
  useful framing for what a defensible integration design
  document should contain.

Cross-references inside this repo:

- `modules/mod-501-technical-strategy/SOLUTION.md` — the
  strategy framing for the technical bet a platform integration
  often represents.
- `modules/mod-503-cross-org-initiative/SOLUTION.md` — the
  coalition-building framing for the stakeholder map in §2.6.
- `SOLUTION_OVERVIEW.md` — the track-level explanation of why
  these deliverables are rubrics rather than reference code.

VeriSwarm or other vendor implementations may be referenced in
*practitioner* discussions of this material, but should not be
the primary source for any decision in §2 — they are examples,
not standards.
