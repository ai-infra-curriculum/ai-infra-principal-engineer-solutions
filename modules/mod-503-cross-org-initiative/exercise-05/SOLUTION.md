# SOLUTION — Exercise 05: Cross-Org Initiative Presentation

> Read this **after** you have drafted your presentation. The
> solution is an annotated outline (continuing the illustrative
> initiative from exercises 01–04), plus the rubric graders will
> apply. For the module-wide framing see
> [../SOLUTION.md](../SOLUTION.md), especially the
> "Presentation (exercise 05)" section.

## 1. Solution overview

### What the exercise is asking for

A **multi-team review presentation** of a cross-org initiative,
delivered to an audience that includes representatives from
every affected team plus the executive sponsor. The
presentation is not a status update; it is a **political
instrument** that either (a) consolidates the sponsor's visible
endorsement and surfaces outstanding concerns in a managed way,
or (b) fails, in which case the initiative is now harder to
drive than before.

Per the module SOLUTION.md, the presentation is fundamentally
different from a single-team review because the audience
*watches for whether their team's interests are visible*. The
presenter's job is to **hold the room** — neither dismissing
concerns nor capitulating to whoever is loudest.

### What makes this hard

- The content is partly review (what we said we'd do, what we
  did) and partly negotiation (what's shifting, who agrees).
- Asymmetric costs *must* be surfaced on the slide, or a
  team's representative will surface them during Q&A in a way
  the presenter can't shape.
- Q&A is where the politics are. Many "questions" in the room
  are demands dressed as questions. The presenter needs a
  pre-written taxonomy of likely questions and who answers
  each.
- The executive sponsor's role in the room is **public
  endorsement**, not technical commentary. The presenter must
  design a moment for that endorsement to happen.

### Deliverable shape

A presentation artefact — slides or a narrative document the
reviewer could present from — of roughly 12–18 slides, with:

1. **Cover + the ask** (1 slide). What outcome this review is
   asking the room to leave with.
2. **Where we are vs where we said we'd be** (1–2 slides).
3. **What's on track, what isn't, what's changing** (2–3
   slides). Changes named, not buried.
4. **Per-team status** (1 slide per team, 4 slides for the
   illustrative example). Each team's own view, including
   their asymmetric cost acknowledged on-slide.
5. **The three things we need from the room today** (1 slide).
   Concrete asks. Not "feedback."
6. **Risks and what we're doing about them** (1–2 slides).
7. **Q&A primer** (not a slide; notes for the presenter). Who
   fields which kind of question.
8. **Close** (1 slide). The sponsor's endorsement moment.
9. **Appendix slides** (as needed). Detail that doesn't need to
   be presented but must be ready if a question goes deep.

Plus:

- A **speaker-notes track** on every live slide. The slides
  are not the presentation; the notes are.
- A **pre-briefing plan**. Which stakeholders get a 1:1
  preview before the room-review, so no stakeholder is
  surprised in the meeting.

## 2. Worked answer or implementation

The worked example continues the AI/ML security baseline
initiative from exercises 01–04 (illustrative). The
presentation below is the **Q+3 mid-initiative review** (see
`../exercise-04/SOLUTION.md §2.1`).

### Slide 1 — Cover + the ask

**Title.** AI/ML Security Baseline — Mid-Initiative Review
(Q+3)

**One-line ask.** "By the end of this meeting, we want
sponsor-endorsed agreement on (1) the Q+4 R1 timeline for A/B/C,
(2) the Q+5 R1 timeline for D with its planned deferral, (3)
the model-card-generator scope cut landed in Q+2."

**Speaker note.** Open with the ask. The audience should know
in sentence one what decision the meeting is reaching. A
meeting without a named outcome drifts.

### Slide 2 — Where we are vs where we said we'd be

A two-column slide:

- **Where we said we'd be at Q+3.** All four teams at R0.
  Baseline v1.0 frozen. ATLAS suite and model-card generator
  shipped.
- **Where we are at Q+3.** Teams A, B, D at R0 in production;
  Team C R0 landed in Q+3 (one-quarter slip, planned in the
  Q+1 review). Baseline v1.0 frozen on schedule. ATLAS suite
  shipped on schedule; model-card generator shipped at Q+2
  with the auto-generation portion cut (manual template landed
  in Q+1 as the fallback, per risk R1 in `../exercise-04/SOLUTION.md §2.4`).

**Speaker note.** Lead with the facts, including the slips.
Hiding the Team C slip reads worse in the room than admitting
it — Team C's EM is in the audience and will correct the slide
if the presenter understates it. Attribute the Team C slip to
the sharding project, which was in the original risk register,
not to Team C's commitment.

### Slide 3 — On track

- R0 adoption on 3 of 4 teams on schedule.
- Baseline v1.0 structure (NIST AI RMF `govern/map/measure/
  manage` function split) survived review without re-litigation.
- ATLAS suite covering inference-time techniques in production.
- PE community endorsement on the record.

**Speaker note.** Don't skip this slide to look humble. The
room needs to see the initiative as functioning before
listening to the problems.

### Slide 4 — Off track, with next steps

| Item | Status | Plan |
|---|---|---|
| Model-card generator (full auto-generation) | Cut from Q+1; manual template shipped | Auto-generation lands in Q+3; central team's extra 0.3 FTE committed |
| Team C R0 | Slipped Q+2 → Q+3 (planned slip) | R0 in production end of Q+3; R1 target Q+4 unchanged |
| Team D partner-release conflict | Risk R4 fired mid-Q+2; R0 landed anyway | No action; monitor Q+3 release schedule |

**Speaker note.** Every "off track" item has a "plan" column.
A slide of problems without plans surfaces the audience's
anxiety about control; a slide with both answers it.

### Slide 5 — Explicit asymmetries

- Team D paying the highest cost; R1 target Q+5, one quarter
  behind A/B/C. Compensation: 0.5 FTE embedded security
  engineer (ongoing), co-authorship credit on the open-weights
  baseline section, PM sign-off on external disclosure.
- Team C paid higher cost than originally planned (deploy-
  cadence impact + sharding-project overlap). Compensation:
  async review-board agreement realised (deploy cadence
  recovered); ranking-eval dashboard roadmap item preserved
  in Q+4.
- Team B paid the least; no compensation needed. Said out
  loud so the symmetry across the room is public.

**Speaker note.** This slide is the single most important slide
of the deck. Every representative in the room is checking
whether the asymmetries are visible. If this slide is absent,
every subsequent slide is viewed with suspicion.

### Slides 6–9 — Per-team status (one slide each)

Each team's slide follows the same template:

- **Team.** A / B / C / D.
- **Representative in the room.** Named EM + security
  champion. (The presenter names them from the stage; the room
  sees who to look at.)
- **R0 status.** Done / in progress / specific gap.
- **R1 trajectory.** On-track / off-track / scoped-differently.
- **This team's view of the initiative today.** One sentence,
  **pre-agreed with the team's EM in the pre-brief**. Not the
  presenter's view of the team. The team's own words.
- **What this team is asking for.** One specific ask, if any.

**Speaker note (critical).** The team-view line must have
been reviewed with the EM in the pre-brief. If an EM sees
their team's view misrepresented on-slide for the first time
in the room, the presentation fails irrespective of its other
quality.

Illustrative Team C slide text:

> **Team C (search ranking).** EM: C-EM. Champion: C-Sen-1.
> R0 landed end of Q+3 (one-quarter planned slip). R1 trajectory
> on-track for Q+4. Team view (confirmed with C-EM, [pre-brief
> date]): "The async review-board agreement was the right
> trade; we'd do it again. We'd like the scope narrowing on
> MITRE ATLAS inference-time techniques to be re-affirmed in
> the baseline v1.1 update." Ask: re-affirmation of the scope
> decision in v1.1.

### Slide 10 — Three things we need from the room today

1. **Sponsor endorsement of the Q+4/Q+5 split timeline.** VP
   of Engineering, this is the moment.
2. **Baseline v1.1 scope** (Team C's scope-narrowing ask).
   Decision today: yes / no / defer to the Q+4 review.
3. **Model-card generator auto-generation sign-off.** Central
   team's extra 0.3 FTE allocation; sponsor ack needed.

**Speaker note.** Each ask is a yes/no decision, not an
open-ended discussion. The slide forces the room into a
decision mode.

### Slide 11 — Risks (updated from `../exercise-04/SOLUTION.md §2.4`)

- R1 (model-card generator slip) **fired and handled**;
  retiring.
- R2 (ATLAS suite slip): did not fire.
- R3 (Team C R0 slip): **fired and handled**; retiring.
- R4 (Team D partner conflict): fired, R0 landed; monitor Q+3.
- R5 (C-Sen-2 drop-out): did not fire; C-Sen-2 contributed the
  inference-surface authentication section on schedule.
- R6 (sponsor unavailability): did not fire.
- R7 (checkbox compliance): *new signal*. The post-initiative
  retro at Q+5 must evaluate adoption quality; proposing we
  add an interim quality check at Q+4 end.
- R8 (external framework change): no change to NIST AI RMF,
  OWASP ML Top 10, or MITRE ATLAS in the window; version pin
  holds.

**Speaker note.** The *new* R7 signal is the honest one.
Surfacing a new risk at the mid-review (vs hiding it) signals
that the initiative is paying attention.

### Slide 12 — Close

**Title.** What we leave this meeting with.

**Content.**

- Sponsor-endorsed timeline (Q+4 R1 for A/B/C, Q+5 for D).
- Decision on Team C's v1.1 scope ask.
- Central team's extra 0.3 FTE allocation confirmed.
- Next review: Q+4 end, with the quality-check item from R7
  added to the agenda.
- Thanks to the four security champions (named). Credit is
  cheap; use it.

**Speaker note.** End with a concrete list of what the room
decided. The last slide should be screenshot-able and emailable
within five minutes of the meeting ending.

### 2.1 Q&A primer (not a slide — presenter notes)

A pre-written Q&A primer is as important as the slides. The
primer lists likely questions, classifies each, and assigns the
responder.

**Classification.**

- **Clarification.** Someone wants a fact restated. Presenter
  answers.
- **Scoping disagreement.** Someone believes the baseline is
  over-scoped or under-scoped. Presenter answers, defers to the
  security engineering staff reviewer for depth if needed.
- **Prioritisation complaint.** Someone believes their team is
  paying too much. Presenter acknowledges, points to the
  asymmetric-cost slide, and if genuinely new, offers to take
  it to the Q+4 review; **does not re-negotiate live**.
- **Political question dressed as technical.** Someone with a
  history of opposing the initiative frames a technical
  objection that is really a positional claim. Presenter
  answers the technical surface briefly, declines to re-open
  the positional argument in the room, and offers a 1:1.
- **Sponsor-escalation request.** Someone explicitly asks the
  sponsor to intervene. This is **the sponsor's moment**;
  presenter steps back.

**Illustrative pre-written answers.**

> Q (Team C PM): "If Team C is accepting the scope narrowing,
> why are we still paying more than Team B?"
>
> A: "The scope narrowing addresses the ATLAS inference-time
> techniques that don't apply to internal search surfaces.
> Team C's remaining baseline-work volume is still larger than
> Team B's because Team C has two production systems and Team
> B has one. We can walk through the per-system inventory in
> the appendix if that's useful."

> Q (C-Sen-2, historical blocker): "I still think the
> model-card generator requirement is over-engineered for
> internal-only models."
>
> A: "That's a real concern and it's partly why we cut auto-
> generation to a manual template in Q+1. We've found the
> manual template is light enough for internal-only models;
> we'd welcome your review of the v1.1 template before it
> freezes. I'll follow up with a 30-minute slot this week."

**Speaker note.** The right answer to a political question is
usually **not** the technical rebuttal. It is a 1:1 offer that
moves the argument off-stage.

### 2.2 Pre-briefing plan

Hold a 1:1 with each of these stakeholders **before** the
room-review. The order and content matters:

1. **VP of Engineering (sponsor), 30 min.** Walk through the
   three asks. Confirm the sponsor is willing to make the
   endorsement moment on slide 10 and 12. If they are not
   willing to endorse any of the three asks, rewrite slide 10
   before the meeting.
2. **VP-Sec, 20 min.** Confirm the baseline v1.1 scope-
   narrowing decision; secure their public neutrality in the
   room.
3. **Each EM, 20 min each.** Review their team's slide
   (slides 6–9). Confirm the team-view line. This is the
   single most important pre-brief; see speaker note on those
   slides.
4. **Team C EM, extra 15 min.** Confirm the planned-slip
   framing. The audience will watch for whether Team C feels
   fairly characterised.
5. **Team D EM + PM, extra 15 min.** Confirm the external-
   disclosure boundary is intact on the asymmetries slide.

If a pre-brief surfaces an objection the presentation can't
absorb, **delay the room-review** rather than delivering a
presentation an EM is going to litigate live.

## 3. Validation steps

A finished presentation passes when all of the following are
true:

1. **The ask slide (slide 1 / slide 10) is specific and
   decidable.** Not "feedback," not "alignment." Named asks
   with yes/no outcomes.
2. **Asymmetric costs are on a slide.** Hiding them is the
   most common reason these presentations fail.
3. **Each team has a slide with its own view** (confirmed in
   the pre-brief), not the presenter's narration of the team.
4. **The sponsor's endorsement moment is designed into the
   deck.** A specific slide where the sponsor is expected to
   speak. Without this, the sponsor either doesn't endorse or
   endorses off-camera where no one hears it.
5. **A Q&A primer exists in speaker notes.** Pre-written
   answers for the three or four most predictable hostile
   questions.
6. **A pre-briefing plan exists.** Named stakeholders, 1:1
   time, order.
7. **The risks section is honest, including new signals since
   the last review.** A risks slide that only retires risks
   and adds none is lying.
8. **The close slide is screenshot-able and emailable.** The
   first thing an EM will do after the meeting is tell their
   team what was decided; the deck must support that.
9. **Every framework reference is sourced.** NIST AI RMF, OWASP
   ML Top 10, and MITRE ATLAS citations resolve to the official
   project pages.

Dry-run test: have the presenter rehearse with the security
engineering staff reviewer playing the role of the historical
blocker (C-Sen-2). If the presenter capitulates or dismisses,
rehearse again until they can hold the room.

## 4. Rubric or review checklist

Grade on a 0–3 scale per criterion. Pass threshold: ≥18/27,
with no criterion at 0.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Ask specificity | "Get feedback" | Ask named | Ask is yes/no | Ask is yes/no + sponsor-endorsed outcome visible |
| 2 | Asymmetric costs on-slide | Absent | Mentioned in notes | On a slide | On a slide + compensation named |
| 3 | Per-team slides | Centralised deck | One per team | One per team with team-view confirmed in pre-brief | One per team, pre-briefed, with named EM and champion |
| 4 | Sponsor endorsement moment | Absent | Mentioned | Slide designed for it | Slide + pre-brief with sponsor confirming willingness |
| 5 | Q&A primer | Absent | Present | Covers predictable hostile questions | Pre-written answers for the political-question-as-technical case |
| 6 | Pre-briefing plan | Absent | Mentioned | Named stakeholders | Named + ordered + with a "delay-the-review" trigger |
| 7 | Risk honesty | Only retires risks | Mixed | Includes new signals | Includes new signals + proposes an interim quality check |
| 8 | Close slide | Thanks and exit | Decisions summarised | Decisions + next-review date | Decisions + next-review + credit to named champions |
| 9 | Would it survive a hostile audience? | No | Partially | Mostly | Yes — the presenter could absorb the predictable political challenges without re-opening the memo |

Reviewer notes:

- Grade down any presentation that scores 3 on §1 but 0 on
  §2. Specific asks without visible asymmetries read as
  manipulative — the room will notice and the deck will fail
  in Q&A.
- A student whose deck has no Q&A primer is usually
  under-practised at multi-team reviews. Coach them through
  the primer exercise rather than regrade; the primer is the
  single highest-leverage piece of practice.

## 5. Common mistakes

1. **The status-update deck.** The presenter treats the review
   as a report, not a decision point. Fix: slide 1 and slide
   10 force the room into decision mode.
2. **Asymmetric costs omitted.** The presenter avoids
   surfacing the uncomfortable team-level asymmetries. The
   affected EM surfaces them during Q&A, from a position of
   grievance, which the presenter now has to defuse live.
   Fix: put the asymmetries on-slide and own them.
3. **Per-team slides written by the presenter, not
   pre-briefed with the team.** An EM sees their team
   mischaracterised in the room. Fix: pre-brief every per-team
   slide.
4. **Sponsor endorsement implicit.** The presenter *assumes*
   the sponsor will say something supportive. The sponsor is
   distracted, says nothing, the room draws conclusions. Fix:
   design a specific slide and a specific line for the
   sponsor; confirm willingness in pre-brief.
5. **Taking political bait in Q&A.** A historical blocker
   frames a technical question that is really a positional
   argument; the presenter re-opens the entire scoping
   decision in the room. Fix: pre-written 1:1 offer. Move the
   argument off-stage.
6. **Risks section that only retires risks.** Hiding new
   signals so the initiative looks healthier than it is. Fix:
   a healthy risks slide adds as well as retires.
7. **No pre-briefing plan.** The presenter walks into the
   room cold. Fix: name every 1:1, in order.
8. **Close slide that thanks-and-exits without listing
   decisions.** EMs leave the room with their own
   interpretations. Fix: screenshot-able close.
9. **Framework citations dropped from the deck.** The
   baseline's grounding in NIST AI RMF, OWASP ML Top 10, and
   MITRE ATLAS was the legitimising claim in the memo; the
   presentation drops it. Fix: at least one slide (typically
   slide 3) names the frameworks the baseline is built on,
   with citations in the speaker notes.

## 6. References

Official sources used to anchor the illustrative baseline (as
cited in the presentation itself, especially slides 3 and 11):

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
- MITRE ATLAS —
  <https://atlas.mitre.org/>.

Internal cross-references:

- [../SOLUTION.md](../SOLUTION.md) — module-level framing.
  The "hold the room" responsibility, the asymmetric-costs-
  on-stage requirement, and the sponsor-endorsement framing
  are from the module SOLUTION.
- [../exercise-02/SOLUTION.md](../exercise-02/SOLUTION.md) —
  the memo whose decisions this presentation is defending
  (and occasionally amending).
- [../exercise-03/SOLUTION.md](../exercise-03/SOLUTION.md) —
  the stakeholder map that drives the pre-briefing plan.
- [../exercise-04/SOLUTION.md](../exercise-04/SOLUTION.md) —
  the roadmap whose Q+3 mid-initiative review this
  presentation delivers.
- [../../mod-502-mentorship-leadership/SOLUTION.md](../../mod-502-mentorship-leadership/SOLUTION.md) —
  the trust-and-relationship layer that makes pre-briefs
  productive; a cross-org presentation without pre-brief
  trust is a presentation that will fail in Q&A.
