# SOLUTION — Exercise 04: Cross-Org Initiative Roadmap

> Read this **after** you have drafted your roadmap. The
> solution is a worked example (continuing the illustrative
> initiative from exercises 01–03), plus the rubric graders will
> apply. For the module-wide framing see
> [../SOLUTION.md](../SOLUTION.md), especially the
> "Roadmap (exercise 04)" section.

## 1. Solution overview

### What the exercise is asking for

A **per-team, synchronised roadmap** for a cross-org initiative:
every affected team has its own track with its own milestones;
synchronisation points show where one team's progress unblocks
another's; risks are named explicitly per team, including risks
that come from the team's own other commitments.

Per the module SOLUTION.md, the roadmap is **not an engineering
artefact alone** — it is a **commitment artefact**. Each team's
leadership must have signed off on the specific timeline. A
roadmap without that is wishful.

### What makes this hard

- The urge is to produce a centralised Gantt chart that treats
  the teams as cells in the same spreadsheet. That abstracts
  away the per-team realities (other quarterly commitments,
  on-call load, hiring, release cycles) that actually govern
  whether each team hits its milestones.
- Synchronisation points are where cross-org roadmaps die.
  Naming them in advance — and naming the recovery path if a
  synchronisation point is missed — is the difference between
  a plan and a wish.
- "Risks" that are only engineering risks miss the point.
  Cross-org risk is dominated by **the team's other
  commitments**, which the initiative owner doesn't control.
  The roadmap must surface these.

### Deliverable shape

A ~4–7 page markdown document containing:

1. **Header block** — the initiative, the sponsor, the
   calendar window (which quarters), the signatories (per-team
   leadership and the central sponsor).
2. **Per-team tracks** — one subsection per affected team,
   with their milestones in their own quarters, their named
   deliverables, their owner, and their **other commitments
   that compete for the same engineers**.
3. **Synchronisation matrix** — a parseable table showing
   which team's output blocks which team's next milestone.
4. **Risk register** — per-team risks (own-team) and
   cross-team risks (dependency risks); each risk has an owner
   and a planned mitigation if it fires.
5. **Recovery plan** — what the initiative owner does if a
   team falls behind. Not a blame session; a documented
   path.
6. **Review cadence** — who looks at this roadmap, how often,
   and what triggers an amendment.
7. **Appendix: sign-offs** — a list of the sign-offs the
   initiative owner has collected, with dates.

## 2. Worked answer or implementation

The worked example continues the AI/ML security baseline
initiative from exercises 01–03 (illustrative). Dates are
relative (Q+0, Q+1, …) so the roadmap is reusable; convert to
absolute dates when adopting.

### 2.0 Header

- **Initiative.** AI/ML Security Baseline rollout (see
  `../exercise-02/SOLUTION.md` for the memo).
- **Sponsor.** VP of Engineering.
- **Calendar window.** Q+0 through Q+5 (6 quarters). R0
  adoption target Q+2; R1 adoption target Q+4 for teams A/B/C,
  Q+5 for team D.
- **Signatories.** Initiative owner (AI platform principal
  engineer); security engineering staff reviewer; EMs of teams
  A, B, C, D; VP of Engineering (sponsor). Dates in Appendix.
- **Roadmap owner.** Initiative owner. Amendments proposed by
  any signatory; approved in the quarterly review (§2.6).

### 2.1 Central track

| Quarter | Milestone | Owner | Done = |
|---|---|---|---|
| Q+0 | Baseline draft v0.9 published for review | Initiative owner | Draft linked in the all-hands deck; VP-Sec and PE community co-review complete |
| Q+0 | Security-champion appointments confirmed | EMs of A/B/C/D | Named engineer per team with 10% time, written in each team's EM-to-team weekly |
| Q+1 | Baseline v1.0 frozen | Initiative owner | Change-control process live; subsequent changes versioned |
| Q+1 | Model-card generator shipped | Central AI platform (1 eng) | Running against at least one production model in dev |
| Q+1 | MITRE ATLAS-aligned test suite shipped | Security engineering (1 eng) | Running against at least one production model in dev |
| Q+2 | R0 adoption across A/B/C/D | Team champions | R0 controls in production per team |
| Q+3 | Mid-initiative review | Initiative owner + VP sponsor | Written review of delta vs plan; memo amended if needed |
| Q+4 | R1 adoption A/B/C | Team champions | R1 controls in production |
| Q+5 | R1 adoption D | Team D champion | R1 controls in production |
| Q+5 | Post-initiative retro | Initiative owner | Retro published; lessons routed to the PE community |

### 2.2 Per-team tracks

#### Team A (recsys)

| Quarter | Milestone | Owner | Known conflicting commitments |
|---|---|---|---|
| Q+0 | Security champion appointed (A-Sen-1) | A-EM | None |
| Q+0 | System inventory + OWASP ML Top 10 gap analysis delivered | A-Sen-1 | None |
| Q+1 | Data-side controls (ATLAS techniques covered by central suite) remediated | A-Staff-2 | Team A is also absorbing on-call from Team X in Q+1; monitor for engineer-week bleed |
| Q+2 | R0 adoption complete; model-card generated | A-Sen-1 | None |
| Q+3 | EU-market pre-clearance review (with VP-Legal) | A-EM + VP-Legal | None |
| Q+4 | R1 adoption complete | A-Sen-1 | Risk: EU launch in Q+5 will consume this team's engineering focus; R1 completion must land before Q+4 end, not slip |

Cost: ~1.0 engineer-quarter. One roadmap item displaced:
"recsys-v3 offline-evaluation harness" moves from Q+2 to Q+3.

#### Team B (content-moderation)

| Quarter | Milestone | Owner | Known conflicting commitments |
|---|---|---|---|
| Q+0 | Security champion appointed (B-Sen-1); baseline adversarial-testing scripts inventoried | B-EM | None |
| Q+1 | Model-card for the primary moderation model | B-Staff-1 | Model v4 launch in Q+1 (positive synergy; the model-card lands with the launch) |
| Q+2 | R0 adoption complete | B-Sen-1 | None |
| Q+4 | R1 adoption complete | B-Sen-1 | None |

Cost: ~0.3 engineer-quarter. No roadmap items displaced
(baseline work overlaps with existing incident follow-up).

#### Team C (search ranking)

| Quarter | Milestone | Owner | Known conflicting commitments |
|---|---|---|---|
| Q+0 | Security champion appointed (C-Sen-1); scope negotiation on ATLAS inference-time techniques | C-EM + C-Staff-1 | **Risk**: C-EM is skeptical; champion appointment likely late in Q+0 |
| Q+1 | Async review-board agreement live (deploy-cadence benefit realised) | C-EM + security engineering staff reviewer | None |
| Q+1 | Inference-surface authentication section co-owned by C-Sen-2 | C-Sen-2 | Risk: C-Sen-2 political history with security engineering; monitor for drop-outs |
| Q+2 | R0 adoption complete | C-Sen-1 | Risk: Q+2 is also the search-infra sharding project; realistic to slip into Q+3 |
| Q+4 | R1 adoption complete | C-Sen-1 | None |

Cost: ~1.0 engineer-quarter. One roadmap item displaced:
"ranking-eval dashboard" moves from Q+3 to Q+4.

#### Team D (open-weights model distribution)

| Quarter | Milestone | Owner | Known conflicting commitments |
|---|---|---|---|
| Q+0 | Security champion appointed (D-Sen-1); open-weights section drafted | D-EM + D-Prin-1 | None |
| Q+1 | Embedded security engineer (0.5 FTE) onboarded | Central security engineering | None |
| Q+1 | Prompt-injection and model-extraction testing section shipped as part of baseline v1.0 | D-Prin-1 | None |
| Q+2 | R0 adoption complete | D-Sen-1 | Risk: partner model-release schedule in Q+2; R0 slip is possible |
| Q+5 | R1 adoption complete (one-quarter deferral per memo §2.4) | D-Sen-1 | None |

Cost: ~1.5 engineer-quarters (asymmetric; compensation named
in memo §2.4). One roadmap item displaced: "distribution-SDK
v2" moves from Q+4 to Q+5.

### 2.3 Synchronisation matrix

| Blocks | Blocked by | What is blocking | Latest-start-to-not-slip |
|---|---|---|---|
| All teams' R0 adoption (Q+2) | Central team: baseline v1.0 freeze (Q+1) | Teams cannot adopt a moving target | Freeze must land by end of Q+1 or R0 slips |
| All teams' R0 adoption (Q+2) | Central team: model-card generator (Q+1) | R0 requires a model-card per system | Generator must run against at least one production model by end of Q+1 |
| All teams' R0 adoption (Q+2) | Central team: ATLAS-aligned suite (Q+1) | R0 adversarial-testing control requires the suite | Suite must run against at least one production model by end of Q+1 |
| Team A's R1 (Q+4) | EU-launch planning (Q+5) | Positive sync, not negative; R1 is a pre-condition | R1 must land by end of Q+4 |
| Team D's R1 (Q+5) | Team D's R0 (Q+2) | Sequential | R0 must land by end of Q+2 |
| PE-community endorsement | Baseline v0.9 draft (Q+0) | Review needs a draft | v0.9 by mid-Q+0 |

### 2.4 Risk register

| # | Risk | Likelihood | Impact | Owner | Mitigation |
|---|---|---|---|---|---|
| R1 | Model-card generator slips past Q+1 | Medium | High (blocks all R0) | Initiative owner | Scope cut: ship manual-template-based model cards as the interim, auto-generation lands by Q+2. Named now so the cut is not a quarter-end surprise. |
| R2 | ATLAS-aligned test suite slips past Q+1 | Medium | High (blocks all R0) | Security engineering staff reviewer | Scope cut: ship inference-time-only coverage in Q+1; training-data coverage in Q+2 |
| R3 | Team C R0 slips to Q+3 | Medium-high | Medium (acceptable if communicated) | Initiative owner + C-EM | Plan the slip explicitly in the Q+1 review rather than surfacing at Q+2 end |
| R4 | Team D R0 slips due to partner release schedule | Medium | Medium | Initiative owner + D-EM | Accept one-quarter slip; move Team D's R0 target from Q+2 to Q+3 if the partner-release conflict is confirmed by end of Q+1 |
| R5 | C-Sen-2 drops out of inference-surface section | Low-medium | Medium (politics) | Initiative owner | Pre-identify a fallback owner (central security engineering staff reviewer picks up the section) |
| R6 | VP-Eng sponsor becomes unavailable | Low | Very high | Initiative owner | Plan for the escalation authority transition up front; VP-Sec is the designated alternate |
| R7 | Baseline is adopted in letter but not spirit (checkbox compliance) | Medium | High (undermines next year's initiative) | Initiative owner | Post-initiative retro (§2.1) must evaluate adoption quality, not just adoption count |
| R8 | External framework change (NIST AI RMF, OWASP, ATLAS) during the window | Medium | Low (the frameworks evolve slowly; our control list is versioned) | Initiative owner | Pin to a specific framework version in the baseline front-matter; re-evaluate at Q+3 mid-review |

### 2.5 Recovery plan

If a team misses a synchronisation point, the initiative owner
runs this sequence, in order:

1. **Diagnose, don't blame.** Inside 48 hours: a written note
   from the team champion describing what caused the slip.
   Common causes: unanticipated on-call load, a central-team
   deliverable not landed, scope that was larger than
   discovered during planning.
2. **Decide: re-plan or escalate.** Re-plan if the cause is
   inside the team's control or inside the initiative owner's
   control. Escalate to the sponsor if the cause is a conflict
   with another commitment the sponsor can adjudicate.
3. **Document the amendment.** The roadmap gets a versioned
   amendment with a dated note. Amendments are additive, not
   silent rewrites; the original dates stay visible.
4. **Share the amendment with every team.** Other teams need
   to know when a synchronisation point moves. Hidden
   amendments break trust faster than missed dates.
5. **Pre-schedule the next check.** A team that slipped once
   has a specific follow-up date three weeks later, not
   "we'll keep an eye on it."

What the recovery plan is not:

- Not a blame session. "Why did you miss?" is the wrong
  question; "what would need to be true for us to hit the
  next milestone?" is the right one.
- Not a silent extension. Extensions must be visible to the
  sponsor and every other team, or the roadmap stops being a
  commitment artefact.

### 2.6 Review cadence

| Cadence | Attendees | Purpose | Trigger for amendment |
|---|---|---|---|
| Weekly (30 min) | Initiative owner + team champions | Tactical; unblock | Any blocker surfaced |
| Monthly (60 min) | Initiative owner + EMs | Commitment health | Any milestone off-track by >2 weeks |
| Quarterly (90 min) | Initiative owner + EMs + VP sponsor | Strategic | Any signatory may propose |
| Ad-hoc | Initiative owner + relevant parties | Risk escalation | Any R1/R2/R6 risk fires |

Amendment process: the proposer files a one-page amendment in
the initiative's shared workspace. Amendments merged in the next
quarterly review unless R6 (sponsor unavailability) or R1/R2
(central deliverable slips) require faster handling.

### 2.7 Appendix — sign-offs

| Signatory | Role | Date signed | Signed version |
|---|---|---|---|
| Initiative owner | AI platform principal eng | [date] | v1.0 |
| Security eng staff reviewer | Central | [date] | v1.0 |
| A-EM | Team A | [date] | v1.0 |
| B-EM | Team B | [date] | v1.0 |
| C-EM | Team C | [date] | v1.0 |
| D-EM | Team D | [date] | v1.0 |
| VP of Engineering | Sponsor | [date] | v1.0 |

The sign-off table is the roadmap's **commitment artefact**.
A roadmap without sign-offs is a plan, not a commitment.
Missing sign-offs are themselves information: the team whose EM
didn't sign is the team whose participation is at risk.

## 3. Validation steps

A finished roadmap passes when:

1. **Every affected team has its own track** with dated
   milestones, named owners, and a column surfacing conflicting
   commitments.
2. **Synchronisation points are explicit.** The synchronisation
   matrix exists as a parseable table (not buried in prose).
3. **Risks are per-team and include non-engineering causes.**
   Only engineering risks named = incomplete.
4. **Recovery plan exists and does not use the word "blame".**
   Recovery is a process, not a punishment.
5. **Review cadence is specified at three levels** (weekly,
   monthly, quarterly) with attendees and triggers.
6. **Sign-offs are tabulated.** A roadmap without sign-offs is
   a draft, not a commitment artefact.
7. **The central team's deliverables are on the roadmap too.**
   A roadmap that only tracks the product teams and quietly
   assumes the central team ships on time is one of the most
   common failure modes.
8. **Asymmetric timelines are visible.** The illustrative
   example has Team D's R1 at Q+5 vs others at Q+4; this must
   be visible, not papered over to look uniform.

Dry-read test: give the roadmap to an EM who was *not*
involved in drafting. Ask: can you tell me what your team is
on the hook for, and when? If they cannot, the per-team track
is insufficiently concrete.

## 4. Rubric or review checklist

Grade on a 0–3 scale per criterion. Pass threshold: ≥18/27,
with no criterion at 0.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Per-team tracks | Centralised Gantt | Per-team sections | Per-team with owners | Per-team with owners + conflicting-commitments column |
| 2 | Synchronisation matrix | Missing | Prose | Table | Table + latest-start-to-not-slip |
| 3 | Risk register | Engineering-only | Mixed | Team + central risks | Team + central + politics + external-framework risks, each with an owner |
| 4 | Recovery plan | Missing | Present | Specific process | Specific process + explicit non-blame framing + visibility requirement |
| 5 | Review cadence | Missing | One level | Two levels | Three levels + amendment process |
| 6 | Sign-offs | Missing | Listed | Tabulated | Tabulated + dates + signed version |
| 7 | Central team on roadmap | No | Mentioned | Milestones listed | Full track, including scope-cut contingencies |
| 8 | Asymmetric timelines | Hidden | Mentioned | Visible | Visible + justified against memo §2.4 |
| 9 | Would it survive the first slip? | No | Partially | Yes | Yes — the recovery plan is specific enough to execute without the initiative owner re-planning in real time |

Reviewer notes:

- A roadmap that scores 3 on §7 (central team on roadmap)
  deserves extra credit. It is the most-often-skipped section
  and the one most responsible for hidden-slip failure modes.
- A roadmap with sign-offs from every signatory but no dates
  on them reads as fabrication. Push the student to say "sign-
  offs are TBD at [date]" rather than leaving the dates blank.

## 5. Common mistakes

1. **Centralised roadmap that hides team-level commitments.**
   Everything in one Gantt, with the teams as abstract rows.
   Fix: produce per-team tracks that each team's EM can recognise
   as their own commitment.
2. **Central team not on the roadmap.** The initiative owner
   does not track their own deliverables; when they slip, it
   is a surprise to everyone. Fix: §2.1 exists in every
   cross-org roadmap.
3. **No conflicting-commitments column.** The team's own
   other commitments are the dominant source of slippage.
   The roadmap must surface them.
4. **Risks that are only engineering risks.** No political
   risks, no framework-version risks, no on-call-load risks.
   Fix: the illustrative risk register in §2.4 covers eight
   kinds for a reason.
5. **No recovery plan.** When a team slips, the initiative
   owner invents a response on the spot. Fix: §2.5 pattern.
6. **No kill criterion in the plan.** Already covered in the
   memo (`exercise-02`); the roadmap must reflect it.
7. **No explicit review cadence.** A roadmap without review
   dates is a plan written once and never revisited. Fix:
   three cadences.
8. **Sign-offs left blank.** Either collect them or say "TBD
   by [date]". Silent blanks read as fictional commitment.
9. **Asymmetric timelines hidden to look uniform.** Hiding
   Team D's one-quarter R1 deferral so the roadmap "looks
   aligned." Fix: the asymmetry is in the memo; it must be
   in the roadmap too.

## 6. References

Official sources used to anchor the illustrative initiative
(mainly in §2.4 R8 and in the baseline-content references
passed through from the memo):

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
- MITRE ATLAS —
  <https://atlas.mitre.org/>.

Internal cross-references:

- [../SOLUTION.md](../SOLUTION.md) — module-level framing.
  The "commitment artefact" framing and the "per-team +
  synchronised" structure are from the module SOLUTION.
- [../exercise-02/SOLUTION.md](../exercise-02/SOLUTION.md) —
  the memo whose dependency graph this roadmap implements.
- [../exercise-03/SOLUTION.md](../exercise-03/SOLUTION.md) —
  the stakeholder map whose movement plan feeds the Q+0
  milestones here.
- [../exercise-05/SOLUTION.md](../exercise-05/SOLUTION.md) —
  the presentation that the Q+3 mid-initiative review
  milestone (§2.1) will ultimately deliver.
- [../../mod-501-technical-strategy/SOLUTION.md](../../mod-501-technical-strategy/SOLUTION.md) —
  the strategic-plan framing; a cross-org roadmap is a
  strategy-plan with sign-offs attached.
