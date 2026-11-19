# SOLUTION — Exercise 03: Cross-Org Stakeholder Mapping

> Read this **after** you have drafted your stakeholder map.
> The solution is a worked example (tied to the illustrative
> initiative from exercises 01 and 02), plus the rubric graders
> will apply. For the module-wide framing see
> [../SOLUTION.md](../SOLUTION.md), especially the
> "Stakeholder mapping (exercise 03)" section.

## 1. Solution overview

### What the exercise is asking for

A **stakeholder map** for a cross-org initiative that goes
beyond "who is affected" to answer four questions per
stakeholder:

1. **Where are they today?** Position on the initiative: ally,
   neutral, skeptic, blocker.
2. **Where do they need to be for the initiative to succeed?**
   Not everyone needs to be an ally. A neutral skeptic who
   lands at "neutral, non-blocking" is a win.
3. **What is the delta?** The work the initiative owner will
   do to move them.
4. **What are they giving you, and what are you giving them
   back?** Cross-org work is reciprocal; the map must make the
   reciprocity visible.

Per the module-level SOLUTION.md, the map is **per team + a
central layer**: every affected team gets its own entry for
engineering manager, senior engineers (individually, not as a
monolith), and product manager; and a central layer covers
executive sponsor, VP-level arbiters, and the principal-engineer
community whose buy-in legitimises the technical approach.

### What makes this hard

- The map is tempting to produce as a title-level org chart.
  That is useless. The work is in individual-level mapping.
- Senior engineers within one team are often **not** a
  monolith. One skeptic can derail a team; one champion can
  carry a team. The map must name them individually.
- Product managers are frequently skipped because the
  initiative owner thinks of the work as "engineering."
  Product managers have effective veto power over roadmap
  space; omitting them is the single most common way
  stakeholder maps underpredict friction.
- Honest "where they are today" positions can be
  uncomfortable. A map that says everyone is already an ally
  is wrong, or is being read by the wrong audience.

### Deliverable shape

A ~4–7 page markdown document with:

1. **Legend** — definitions of the four positions (ally,
   neutral, skeptic, blocker) in your own words, so the reader
   knows what you mean.
2. **Per-team map** — one subsection per affected team, with
   EM, each named senior engineer, PM, and (where relevant)
   the on-call/operational lead. Each stakeholder gets the
   four-question treatment.
3. **Central layer** — executive sponsor, VP arbiters, and the
   principal-engineer community.
4. **Reciprocity ledger** — for each team: what you are asking
   for, what you are giving back. Not optional.
5. **Movement plan** — for each stakeholder currently off their
   target position, one specific intervention and its owner.
6. **Audience statement** — who this map is for, and who it is
   deliberately not shared with. Maps that say "skeptic" about
   a named individual are not for team-wide distribution; they
   are working artefacts for the initiative owner.

## 2. Worked answer or implementation

The worked example continues the AI/ML security baseline
initiative from exercises 01 and 02 (illustrative, not a specific
company). Names are illustrative placeholders; the shape of the
analysis is the deliverable.

### 2.0 Legend

- **Ally.** Will actively advocate for the initiative inside
  their team or forum. Will own a specific deliverable or
  position.
- **Neutral (non-blocking).** Has no energy to spend
  advocating, will not oppose. Will accept well-written plans
  without litigation.
- **Neutral (prove-it).** Not opposed, but expects the
  initiative to demonstrate value before they commit their
  team's time. Needs an early concrete win.
- **Skeptic.** Has specific, substantive objections. Treat
  them as information, not obstruction (module SOLUTION.md
  "common mistakes" §2).
- **Blocker.** Will actively oppose. Rare; often confused with
  skeptic. True blockers usually have a political reason, not
  a technical one.

### 2.1 Per-team map

#### Team A (recsys) — ~15 engineers

| Role | Name | Today | Target | Delta |
|---|---|---|---|---|
| EM | A-EM | Neutral (prove-it) | Ally | Walk through the EU-market benefit specifically; tie baseline work to Q+5 launch gating. |
| Staff eng (ML systems) | A-Staff-1 | Neutral (non-blocking) | Neutral (non-blocking) | No change needed. |
| Staff eng (data pipelines) | A-Staff-2 | Skeptic (believes OWASP ML Top 10 data-poisoning controls are redundant with existing data-validation layer) | Neutral (prove-it) | 90-min working session walking through the specific MITRE ATLAS techniques not covered by existing validation. Likely outcome: A-Staff-2 becomes the local technical owner of the data-side controls. |
| Senior eng (serving) | A-Sen-1 | Ally (previously worked on security initiative at prior company) | Ally | Ask them to be security champion. |
| PM | A-PM | Neutral (prove-it) | Neutral (non-blocking) | Share the Q+5 regulatory pre-clearance argument; confirm with their legal partner that the baseline is a path, not a detour. |

**Reciprocity.** We are asking Team A for one engineer-quarter
of effort and for displacing one planned roadmap item. We are
giving back: the EU-launch pre-clearance narrative, a dedicated
review slot on the central team's calendar for their questions,
and co-authorship credit on the baseline for whichever senior
engineer owns the data-side section.

#### Team B (content-moderation) — ~12 engineers

| Role | Name | Today | Target | Delta |
|---|---|---|---|---|
| EM | B-EM | Ally | Ally | Already committed post-incident. Keep informed, don't over-ask. |
| Staff eng (ML core) | B-Staff-1 | Ally (owned the incident retro) | Ally | Nominate as the baseline's model-evaluation reviewer. |
| Senior eng (adversarial testing) | B-Sen-1 | Ally | Ally | Invite to co-author the MITRE ATLAS-aligned test suite — they have practitioner expertise. |
| PM | B-PM | Neutral (non-blocking) | Neutral (non-blocking) | No change needed. |

**Reciprocity.** We are asking Team B for the least new effort
(they did most of this work after the Q-1 incident). We are
giving back: a defensible audit-trail artefact for the incident
follow-up, and the tooling will absorb their internal
adversarial-testing scripts rather than replacing them.

#### Team C (search ranking) — ~20 engineers

| Role | Name | Today | Target | Delta |
|---|---|---|---|---|
| EM | C-EM | Skeptic (concerned about deploy-cadence impact) | Neutral (non-blocking) | Lock in the async review-board agreement in writing, before memo publication. Without that, C-EM's skepticism is well-founded. |
| Staff eng (search platform) | C-Staff-1 | Skeptic (asserts MITRE ATLAS inference-time techniques are mostly irrelevant to internal search surfaces) | Neutral (prove-it) | Joint threat-modelling session with security engineering to narrow ATLAS coverage to the inference-time techniques actually applicable; accept that the scope here may legitimately be smaller than Team D's. |
| Senior eng (ranking models) | C-Sen-1 | Neutral (non-blocking) | Ally | Offer the security-champion role. If they decline, ask them to recommend who. |
| Senior eng (serving) | C-Sen-2 | Blocker (has publicly opposed the initiative in a prior forum; political, not technical — believes security engineering oversteps) | Neutral (non-blocking) | Do not attempt to convert to ally. Target is to neutralise. Mechanism: give them a specific, bounded technical ownership (e.g., owning the inference-surface authentication section of the baseline) that converts their expertise into contribution. |
| PM | C-PM | Neutral (prove-it) | Neutral (non-blocking) | Attend their roadmap-planning session in week 2 and present the async-review-cadence benefit directly. |

**Reciprocity.** We are asking Team C for the second-highest
cost (medium-high). We are giving back: the async-review
agreement (a measurable deploy-cadence benefit), scope
narrowing on ATLAS coverage to what is actually applicable,
and public credit for C-Sen-2's contribution on the baseline.

The Team C row is deliberately the longest. Team C is where
this initiative is most likely to stall, and the map must
show that.

#### Team D (open-weights model distribution) — ~15 engineers

| Role | Name | Today | Target | Delta |
|---|---|---|---|---|
| EM | D-EM | Neutral (prove-it) | Ally | The asymmetric-cost compensation (§2.4 of the memo) is the lever. Walk through the embedded-engineer allocation explicitly. |
| Principal eng (model distribution) | D-Prin-1 | Neutral (non-blocking) | Ally | Peer-level conversation; include them as a co-author of the open-weights section of the baseline. Open-weights threat modelling is their domain expertise, not security engineering's. |
| Senior eng (release tooling) | D-Sen-1 | Ally | Ally | Nominate as security champion. |
| PM | D-PM | Skeptic (concerned about partner-communication fallout if baseline surfaces exposure publicly) | Neutral (prove-it) | Agree on disclosure guidance: internal baseline conformance is not published externally without PM sign-off on the message. |

**Reciprocity.** We are asking Team D for the highest cost. We
are giving back: an embedded security engineer (0.5 FTE) for
Q+1 and Q+2, a planned one-quarter deferral on R1 targets,
co-authorship of the open-weights section of the baseline, and
PM sign-off on any external disclosure.

### 2.2 Central layer

| Role | Name | Today | Target | Delta |
|---|---|---|---|---|
| Executive sponsor (VP Eng) | VP-Eng | Ally | Ally | Monthly 30-minute sync; **budget one explicit use of sponsor authority for conflict resolution** (see memo §2.7). If we don't use the sponsor once visibly, we're not really sponsored. |
| VP arbiter (Security) | VP-Sec | Neutral (prove-it) | Ally | Pre-review the baseline before memo publication; adopt their feedback visibly. Their endorsement halves the resistance from product-team PMs. |
| VP arbiter (Legal/Compliance) | VP-Legal | Neutral (non-blocking) | Ally | Position the baseline as evidence of due diligence for EU-market and partner-contract audits. Low effort, high leverage. |
| Principal-engineer community | PE-Community | Mixed (3 allies, 4 neutral, 2 skeptics) | Majority ally | Present the baseline at the next monthly PE forum; invite the two skeptics to co-review the draft. Peer legitimacy is what makes this initiative survive without the sponsor having to carry it. |

### 2.3 Movement plan (consolidated)

Sorted by priority (highest-leverage moves first):

| # | Target | Intervention | Owner | By when |
|---|---|---|---|---|
| 1 | VP-Sec → Ally | Pre-publication baseline review | Initiative owner | Pre-memo |
| 2 | C-Sen-2 (blocker → neutral) | Bounded technical ownership offer | Initiative owner + C-EM | Week 1 |
| 3 | C-EM → neutral (non-blocking) | Async review-board agreement in writing | Initiative owner + security engineering staff reviewer | Pre-memo |
| 4 | A-Staff-2 (skeptic → neutral prove-it) | Technical working session on uncovered ATLAS techniques | Initiative owner | Week 2 |
| 5 | D-EM → ally | Walk through embedded-engineer allocation | Initiative owner + VP-Eng sponsor | Week 1 |
| 6 | PE-community skeptics → neutral | Draft co-review invitation | Initiative owner | Week 2 |
| 7 | D-PM → neutral prove-it | Disclosure-guidance agreement | Initiative owner + D-PM | Week 3 |

### 2.4 Audience statement

This map is a **working artefact** for the initiative owner and
the executive sponsor. The per-stakeholder positions (ally /
skeptic / blocker) are **not** suitable for distribution to the
teams being mapped. A sanitised version — reciprocity ledger
plus movement plan without per-person labels — can be shared
with each team's EM so they see what the initiative owner is
asking for and what is being offered back.

Writing uncomfortable truths in the working artefact is
correct. Sharing them with the subject is not.

## 3. Validation steps

A finished map passes when all of the following are true:

1. **Every affected team has its own section.** No team-level
   summary that stands in for individual-level mapping. The
   module SOLUTION.md's "don't treat senior engineers as a
   monolith" failure mode is the most-tested item.
2. **Each team's section names the EM, the senior/staff
   engineers individually, and the PM.** PMs skipped is the
   most common failure.
3. **Every stakeholder has a current position, target
   position, and delta.** A stakeholder with no delta is
   either (a) already on-target (fine; say so), or (b) not
   thought about (not fine; the map is incomplete).
4. **The reciprocity ledger exists per team and specifies
   both directions.** "What we are asking / what we are
   giving" with specific items. A ledger that lists only what
   we are asking for is a demand, not a map.
5. **The central layer includes the executive sponsor, at
   least one VP-level arbiter, and the principal-engineer
   community.** Any missing layer is a specific blind spot.
6. **At least one stakeholder is labelled skeptic or blocker,
   and the delta describes a specific movement plan.** A map
   with no skeptics and no blockers is wrong. The real org
   contains disagreement; a map that hides it will miss the
   friction when it surfaces.
7. **The movement plan has owners and deadlines.** A plan with
   no owners is a wishlist.
8. **The audience statement exists.** Maps that name
   individual positions must be explicit about who they are
   for and who they are not for.

Dry-read test: hand the map to the executive sponsor. Do they
know what you need from them, by when? If not, the central
layer is underdeveloped.

## 4. Rubric or review checklist

Grade on a 0–3 scale per criterion. Pass threshold: ≥18/27,
with no criterion at 0.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Per-team coverage | Monolithic | Teams listed | Each team has EM + senior + PM | Each team has EM + each senior individually + PM + operational lead where relevant |
| 2 | Position honesty | All allies | Some skeptics | Skeptics named with substantive reasons | Skeptics and at least one blocker named; politics vs technical distinction made |
| 3 | Delta specificity | "Influence them" | Delta named | Specific intervention | Specific intervention + likely outcome |
| 4 | Reciprocity | One-sided | Mentioned | Per-team ledger | Per-team ledger with asymmetric cost compensations surfaced |
| 5 | Central layer | Missing | Sponsor only | Sponsor + VP arbiter | Sponsor + VP arbiter + PE community + positions and deltas for each |
| 6 | Movement plan | Prose | Table | Table with owners | Table with owners, deadlines, and priority-ordered |
| 7 | Audience statement | Missing | Mentioned | Explicit "for / not for" | Explicit + describes the sanitised version for team-wide distribution |
| 8 | Honesty about politics | Scrubbed | Mentioned | Named | Named + a political-vs-technical blocker distinction with a specific handling strategy |
| 9 | Would it survive contact with the org? | No | Partially | Mostly | Yes — the initiative owner could execute from this map tomorrow |

Reviewer notes:

- A student who cannot name a single skeptic is either too
  junior to have shipped a cross-org initiative (grade with
  compassion, push them to interview someone who has), or is
  self-editing for fear of writing something unflattering.
  Both cases require coaching before grading.
- A map with a strong Team C-style section (the team most
  likely to stall the initiative) is a higher-quality
  artefact than one with four roughly-equal sections, even
  if the total length is the same.

## 5. Common mistakes

1. **Senior-engineer monolith.** Mapping each team's "senior
   engineers" as a single row. Fix: one row per named senior.
   This is the module SOLUTION.md's first-listed failure mode
   for this exercise, and the most-seen in practice.
2. **PMs skipped.** The map is "the engineering stakeholder
   map." Fix: PMs have roadmap veto power; they must be
   mapped.
3. **No blockers, no skeptics.** Everyone is an ally or
   neutral. Fix: force yourself to put one skeptic per team,
   with a specific objection; if you cannot, the map was
   written to a different audience than it should have been.
4. **Deltas as vibes.** "Build rapport," "influence." Fix:
   name the specific intervention, by whom, by when, with
   the expected outcome.
5. **No reciprocity ledger.** The map lists what you want
   from each team but not what each team gets back. Fix:
   every team gets a reciprocity sentence, specifically.
6. **Central layer thin.** Only the sponsor is mapped. Fix:
   VP arbiters and the PE community are the legitimising
   layer; without them, the sponsor has to carry the
   initiative alone and will burn out on it.
7. **Audience confusion.** The uncomfortable artefact is
   distributed team-wide, and a conversation blows up. Fix:
   the audience statement is not ceremony; it protects the
   map's honesty.
8. **Converting a political blocker into an ally.** Political
   blockers almost never become allies; the correct target is
   neutral (non-blocking) via bounded technical ownership or
   via sponsor escalation. Trying to convert them wastes
   months.

## 6. References

Official sources used to anchor the illustrative initiative
(mainly relevant for Team D's threat-model scope, Team C's
inference-time technique discussion, and the baseline's control
structure):

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
- MITRE ATLAS —
  <https://atlas.mitre.org/>.

Internal cross-references:

- [../SOLUTION.md](../SOLUTION.md) — module-level framing,
  especially the "per team plus central" structure and the
  failure-mode list.
- [../exercise-02/SOLUTION.md](../exercise-02/SOLUTION.md) —
  the memo whose asymmetric-cost section and
  decision-authority section this map operationalises.
- [../exercise-04/SOLUTION.md](../exercise-04/SOLUTION.md) —
  the per-team roadmap that turns this map's commitments into
  dated milestones.
- [../../mod-502-mentorship-leadership/SOLUTION.md](../../mod-502-mentorship-leadership/SOLUTION.md) —
  the trust-building layer; many of the deltas in this map
  are mentorship moves in disguise (giving a skeptic a
  bounded ownership is the same play as giving a staff
  engineer a stretch assignment).
