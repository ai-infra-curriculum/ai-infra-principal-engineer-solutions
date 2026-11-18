# SOLUTION — Exercise 02: Cross-Org Initiative Strategy Memo

> Read this **after** you have drafted your strategy memo. The
> solution is a worked example you can grade yourself against,
> plus the rubric graders will apply. For the module-wide framing
> see [../SOLUTION.md](../SOLUTION.md), especially the
> "Strategy memo (exercise 02)" section.

## 1. Solution overview

### What the exercise is asking for

A **strategy memo** that commits a cross-org initiative to a
specific plan — small enough to read in 20 minutes, specific
enough that an executive sponsor could sign it, honest enough
that a skeptical staff engineer on an affected team would
accept it even where their team pays more than the average.

The module-level SOLUTION.md defines the seven required pieces
of a cross-org memo (the change, the shared benefit, the shared
cost, the asymmetric costs, the dependency graph, the escape
valves, the decision authority). This exercise asks you to
produce one such memo for a chosen initiative and defend it
against a hostile but informed reader.

### What makes this hard

- The temptation is to write a memo that *sounds good* to the
  sponsor and skips the uncomfortable parts (asymmetric costs,
  escape valves, who-decides-when-teams-disagree).
- The uncomfortable parts are exactly where cross-org
  initiatives stall. A memo that omits them has not survived
  contact with reality.
- "Specific enough to be falsifiable" is harder than it looks.
  "All teams adopt by Q3" is not a plan; it is a wish.

### Deliverable shape

A ~5–8 page markdown document, structured as the seven-piece
memo from `../SOLUTION.md`:

1. **The change** (1 paragraph) — what each team is being asked
   to do.
2. **The shared benefit** (0.5 page) — per-team, not org-level.
3. **The shared cost** (0.5 page) — per-team, honestly.
4. **The asymmetric costs** (0.5–1 page) — which teams pay
   more, why, and how we are compensating them.
5. **The dependency graph** (0.5–1 page + a diagram) —
   sequencing, parallelism, blocking relationships.
6. **The escape valves** (0.5 page) — what a team does if they
   genuinely cannot move on the proposed timeline.
7. **The decision authority** (0.5 page) — who decides when
   teams disagree, before any specific disagreement surfaces.

Plus:

- A one-paragraph **"what success looks like at Q+4"** header
  the sponsor can read without reading the rest.
- An appendix with the kill criteria (when do we wind this
  down?) and the review cadence (who looks at this, how often,
  and what triggers re-opening the memo).

## 2. Worked answer or implementation

The worked example below is the same AI/ML security baseline
initiative introduced in `exercise-01/SOLUTION.md §2.2`
(illustrative, not a specific company). Carrying the example
across exercises lets you see how the memo, stakeholder map,
roadmap, and presentation fit together.

### 2.0 Header — what success looks like at Q+4

> By the end of quarter Q+4, all four product teams that ship
> ML systems have adopted the R0 (required-at-launch) controls
> of the AI/ML Security Baseline. The baseline is a direct
> implementation of NIST AI RMF's `govern / map / measure /
> manage` functions, scoped to the vulnerabilities enumerated
> in OWASP ML Top 10 and the adversarial techniques in MITRE
> ATLAS. R1 (within-one-quarter) controls are tracked publicly;
> partial R1 adoption is acceptable and surfaced in the review.

### 2.1 The change

Each product team shipping an ML system will, by Q+4:

- Document their system against the **govern/map/measure/manage**
  control structure from NIST AI RMF, using the central
  baseline template. This is paperwork, done once per system.
- Run their training pipeline and inference surface against the
  **OWASP ML Top 10** checklist and remediate R0 items. Current
  gap analysis per team is in the appendix.
- Run the **MITRE ATLAS**-aligned adversarial-testing suite the
  central team is shipping in Q+1 against their inference
  surface, at a cadence matching their release frequency.
- Nominate one **security champion** per team (10% time, EM-
  confirmed) who owns local adoption and is the cross-team
  point of contact.

The central team (AI platform + security engineering) owns:

- The baseline document itself.
- The adversarial-testing suite tooling.
- The model-card generator and template.
- The weekly cross-team sync.

### 2.2 The shared benefit

Per team, not per org. (Org-level benefit — "reduced AI risk" —
is not a team-level incentive and should not be the memo's
primary pitch.)

- **Team A (recsys).** Legal/compliance pre-clearance for the
  EU-market expansion in Q+5. The baseline is one of two
  pre-conditions for that launch; getting it done now
  parallelises the regulatory work with the engineering work.
- **Team B (content-moderation).** Post-incident gap closure.
  The baseline formalises the model-input validation that was
  added ad-hoc after the Q-1 incident, and gives the team a
  defensible story for the incident retrospective's audit
  follow-up.
- **Team C (search ranking).** Faster model-approval review.
  The central review board has agreed that baseline-conformant
  changes can be approved asynchronously, a measurable latency
  improvement on Team C's deploy cadence.
- **Team D (open-weights model distribution).** A *credible*
  threat model. Team D is the most exposed to prompt-injection
  and model-extraction attacks; the baseline gives them a
  documented defensive posture to point at when asked about it
  by partners.

Note: the benefit-per-team must be specific enough that an EM
could use it to defend the quarter's commitment to their team.

### 2.3 The shared cost

- Engineer-time per team: approximately 1.0 engineer-quarter
  for R0 adoption on an existing system. Teams with multiple
  production ML systems multiply by system count. Current
  estimates in the appendix.
- Model-release cadence: expected to slow by one additional
  review cycle (~1 week) per release for the first two
  quarters, then return to current cadence as the
  model-card-generator automation lands.
- Roadmap items displaced: each team's EM has identified one
  quarter-item that is deferred to make room. Named in the
  appendix.

### 2.4 The asymmetric costs

This is the section the memo must be most honest about.
Equal-cost assumptions are the single most common reason
cross-org security initiatives stall mid-way through — see
module SOLUTION.md's "equal-cost assumptions" failure mode.

| Team | Relative cost | Why |
|---|---|---|
| A (recsys) | Medium | Standard ML pipeline, authenticated API. OWASP ML Top 10 coverage is mostly data-side; MITRE ATLAS coverage is modest. |
| B (content-moderation) | Low | Already covered most R0 after the Q-1 incident retro. Mostly paperwork. |
| C (search ranking) | Medium-high | Inference surface is partially public (search autocomplete); MITRE ATLAS inference-time technique coverage is substantial. |
| D (open-weights distribution) | **High** | Open-weights exposure makes prompt-injection, model-extraction, and model-poisoning threat surfaces materially larger. OWASP ML Top 10 coverage is near-full; MITRE ATLAS coverage is near-full. |

**Compensation for Team D.** Team D's asymmetric cost is
recognised with:

- A dedicated embedded security engineer (0.5 FTE) from the
  central team for Q+1 and Q+2.
- Explicit permission to defer R1 controls one quarter beyond
  the baseline deadline (so Team D's R1 target is Q+5, not
  Q+4). This is not an "escape valve" (see §2.6); it is a
  planned asymmetry.
- Credit in the Q+2 executive review: the memo's §2 success
  metric will report Team D's R1 progress separately with the
  asymmetry disclosed.

### 2.5 The dependency graph

```
                  [central: baseline doc frozen, Q+0]
                              |
                              v
        +---------------------+---------------------+
        |                     |                     |
        v                     v                     v
 [central: model-card    [central: ATLAS       [all teams: appoint
  generator Q+1]          suite Q+1]            security champion Q+0]
        |                     |                     |
        +--------- blocking for R0 adoption --------+
                              |
                              v
             [teams A, B, C, D: R0 adoption Q+2]
                              |
             +----------------+----------------+
             |                                 |
             v                                 v
 [teams A, B, C: R1 adoption Q+4]      [team D: R1 adoption Q+5]
```

Readable version:

- The central team's two deliverables (model-card generator,
  ATLAS-aligned suite) are **blocking** for team R0 adoption.
  Slippage on either moves R0 adoption by the same amount.
- Team champion appointments are **not** blocking on the
  central deliverables and happen in Q+0.
- Teams A, B, and C adopt R0 in parallel. Team D adopts R0 in
  parallel but adopts R1 one quarter later, as agreed in §2.4.

### 2.6 The escape valves

Named, in-advance exits — not loopholes.

1. **Deferred-system escape.** A team may defer the baseline on
   a specific production ML system if that system is on a
   documented sunset path with a shutdown date no later than
   Q+6. "Deferred" means out of §2.3 cost accounting, but the
   system is listed in the risk register so the sponsor can see
   the exposure.
2. **Phased-coverage escape.** A team may split R0 adoption
   across sub-systems with the central team's agreement, if
   their system has multiple distinct inference surfaces and
   covering them all in one quarter would displace a
   business-critical roadmap item. The split plan must land in
   the weekly sync by Q+1.
3. **Asymmetry escape for Team D.** Already named in §2.4.
   Listed here so it is visible in the escape-valve section
   too; no section should be the only place a team's exception
   lives.
4. **The null escape.** A team that genuinely cannot adopt
   because an upstream dependency (third-party model vendor,
   external data source) refuses to meet the baseline
   escalates to the decision authority (§2.7) with a written
   problem statement. The decision authority may re-scope the
   baseline, extend the timeline, or carry the exposure
   explicitly on the risk register.

### 2.7 The decision authority

- **Technical conflicts** (how to implement a control, which
  OWASP ML Top 10 item applies to a given system, how to
  interpret a NIST AI RMF function in our stack): decided by
  the AI platform principal engineer (initiative owner) in
  consultation with security engineering's staff reviewer.
- **Prioritisation conflicts** (a team believes the cost
  genuinely exceeds the per-team benefit): escalated to the
  VP of Engineering (sponsor) with a one-page written
  position. The sponsor's decision is written down and
  attached to this memo as an addendum.
- **Scope conflicts** (should an adjacent system also be in
  scope): decided by the same path. The memo is a living
  artefact; scope changes are versioned, with a dated changelog
  at the top.
- **Default.** If no one has decided after one week of visible
  disagreement, the initiative owner forces the call and
  documents it. Under-deciding is worse than deciding wrong,
  because it signals that the decision authority isn't real.

### 2.8 Appendices

- **A1. Per-team system inventory.** For each team, each
  production ML system, current R0 gap against OWASP ML Top 10
  and MITRE ATLAS coverage.
- **A2. Displaced roadmap items.** One quarter-item per team.
- **A3. Kill criteria.** The initiative winds down if, by end
  of Q+1, the central team has not shipped either the
  model-card generator or the ATLAS-aligned suite. In that
  case the baseline reverts to documentation-only and the
  org accepts the current exposure explicitly.
- **A4. Review cadence.** Weekly tactical sync (champions +
  initiative owner). Monthly rollup (EMs + initiative owner).
  Quarterly review (VP sponsor + EMs + initiative owner),
  during which this memo is re-opened for amendment if any
  party requests it.

## 3. Validation steps

A finished memo passes when all of the following are true:

1. **All seven required pieces are present and non-trivial.**
   A memo that lists the shared cost as "some engineering
   time" fails the seven-piece check even if the section
   exists.
2. **The benefit is per-team, not org-level.** Every team can
   point to its own row and say "that is the sentence my EM
   can use to defend this commitment."
3. **The asymmetric-cost section names the team paying most
   and the compensation offered.** A memo without an
   asymmetric-cost section fails on sight; a memo whose
   asymmetric-cost section says "all teams pay roughly equally"
   fails unless it defends that claim explicitly.
4. **The dependency graph is visual and parseable.** A diagram
   in ASCII or an image, not a prose list.
5. **Escape valves are named in advance.** "We will work it out
   if issues arise" is not an escape valve.
6. **The decision authority is a named role, with the
   escalation path spelled out.** "Leadership will decide" is
   not decision authority.
7. **Kill criteria are specified.** An initiative without kill
   criteria is a commitment with no exit, which is a
   governance failure, not a planning failure.
8. **Every framework reference is sourced.** NIST AI RMF, OWASP
   ML Top 10, and MITRE ATLAS citations link to the official
   project pages. Framework-content paraphrases that aren't in
   the official sources get `<!-- needs-citation: ... -->`.
9. **Length between ~2000 and ~5000 words.** Shorter is
   underspecified; longer is unread.

Dry-read test: ask another principal engineer who has not seen
the memo to read it and summarise the plan in one minute. If
their summary misses any of the seven pieces, the memo buried
something important.

## 4. Rubric or review checklist

Grade on a 0–3 scale per criterion. Pass threshold: ≥18/27,
with no criterion at 0.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | The change | Vague | Named deliverables | Named deliverables + ownership | Named deliverables, ownership, central vs team split |
| 2 | Shared benefit (per-team) | Org-level | Per-team but generic | Per-team + specific | Per-team, specific, defensible by each team's EM |
| 3 | Shared cost (per-team) | Missing | Qualitative | Engineer-time quantified | Engineer-time + roadmap displacement + cadence impact |
| 4 | Asymmetric costs | Missing | Named | Named + compensation | Named + compensation + referenced in escape-valve section |
| 5 | Dependency graph | Prose only | Diagram, incomplete | Diagram + blocking relationships marked | Diagram + blocking + parallelism + per-team sequencing |
| 6 | Escape valves | None | One, vague | 2–3 specific | ≥3 specific, including the null escape |
| 7 | Decision authority | "Leadership" | Role named | Role + escalation path | Role + escalation path + default when no one decides |
| 8 | Kill criteria | Missing | Present, vague | Present, specific | Present, specific, tied to a date and a central deliverable |
| 9 | Framework citations | Unsourced | Sources named | Sources linked | Sources linked, each used for a specific baseline section |

Reviewer notes:

- A high-grading memo (24+) should be usable as-is to drive
  the initiative. If the reviewer finishes the memo and still
  doesn't know what Team D is signing up for, grade down
  criteria 2–4 even if the writing is strong.
- A memo that scores 3 on §7 (decision authority) but 0 on §8
  (kill criteria) is a specific anti-pattern: the author
  understands conflict resolution but has not planned the
  initiative's exit. Push back and ask them to add kill
  criteria rather than pass them.

## 5. Common mistakes

1. **Org-level benefit masquerading as team-level benefit.**
   "The company will be more secure" is not a team-level
   benefit. Rewrite each benefit until an EM for that specific
   team could point at it to defend the quarter's commitment.
2. **Hand-waved asymmetric costs.** The memo lists the
   asymmetry but offers no compensation. Either compensate or
   accept it explicitly and describe why the team agreed
   anyway (rare, but valid).
3. **Decision authority = "leadership".** Leadership is a
   title, not an escalation path. The memo must name a role
   and describe when they are invoked.
4. **No kill criteria.** A cross-org initiative without a kill
   criterion is signalling that it cannot be stopped, which
   erodes its credibility. Even if the kill criterion is
   never invoked, naming it strengthens the memo.
5. **The seven pieces are present but one of them is a single
   sentence.** The seven-piece check is for presence and
   depth. "The escape valves are: teams can ask for an
   extension" does not count as having escape valves.
6. **Framework name-dropping without use.** Citing NIST AI RMF
   in the header but structuring the controls against some
   other taxonomy is worse than not citing it at all —
   reviewers will ask why the citation is there.
7. **Review cadence not specified.** A memo that commits
   teams to a plan but doesn't commit the initiative owner
   to a review cadence is asymmetric in the wrong direction.

## 6. References

Official sources used to anchor the illustrative baseline:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
  The `govern / map / measure / manage` function split is used
  as the structural spine of the baseline (§2.0, §2.1).
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Used as the per-system vulnerability checklist (§2.1, §2.4).
- MITRE ATLAS —
  <https://atlas.mitre.org/>.
  Used as the adversarial-technique reference for the testing
  suite (§2.1, §2.5).

Internal cross-references:

- [../SOLUTION.md](../SOLUTION.md) — module-level framing.
  The seven-piece memo checklist in §1 is derived directly
  from this.
- [../exercise-01/SOLUTION.md](../exercise-01/SOLUTION.md) —
  the illustrative initiative is introduced there and carried
  forward here.
- [../exercise-03/SOLUTION.md](../exercise-03/SOLUTION.md) —
  the stakeholder map that corresponds to the dependency
  graph and the decision-authority section of this memo.
- [../exercise-04/SOLUTION.md](../exercise-04/SOLUTION.md) —
  the per-team roadmap that makes the dependency graph real.
- [../../mod-501-technical-strategy/SOLUTION.md](../../mod-501-technical-strategy/SOLUTION.md) —
  strategy-memo framing at the single-initiative level; the
  cross-org memo inherits the "what's the bet / what's the
  kill criterion" structure.
