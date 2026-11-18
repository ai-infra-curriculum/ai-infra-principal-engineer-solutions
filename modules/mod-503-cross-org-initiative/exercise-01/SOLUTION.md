# SOLUTION — Exercise 01: Cross-Org Initiative Case Study

> Read this **after** you have attempted the case-study
> deliverable. This solution is a rubric plus a worked example;
> it is not the only passing answer. For the module-wide framing
> see [../SOLUTION.md](../SOLUTION.md), especially the
> "Case study (exercise 01)" section.

## 1. Solution overview

### What the exercise is asking for

A written case study (~3–6 pages) of a real cross-org technical
initiative, examined along four axes: **scope, sponsor, tactics,
outcome**. The point is not to summarise a public engineering
blog — it is to look *through* the public account at the
political and coordination work that made the initiative succeed
or fail. A passing case study answers one question the public
retrospective usually doesn't: *why did this specific
coordination pattern work (or fail) with these specific teams at
this specific company?*

### What makes this hard

- The interesting data is almost always *between the lines* of
  the public account. Companies tell the shipped-cleanly story;
  the near-fails are edited out.
- "Successful" is contested. A migration can succeed technically
  and leave the org too bitter to try the next one. The case
  study must evaluate both the technical and the organisational
  outcomes.
- It is tempting to grade an initiative by its final state. The
  more useful grade is at the **mid-point** — the moment the
  initiative was most likely to stall.

### Deliverable shape

A ~3–6 page markdown document with these subsections:

1. **One-paragraph summary** — one initiative, one sentence on
   what changed, one sentence on whether it worked.
2. **Scope** — teams, engineers, timeline, calendar.
3. **Sponsor** — who at exec level was visibly behind this, and
   what their sponsorship actually *did* (not what the memo said
   it would do).
4. **Tactics** — 3–5 specific moves the driving engineer(s) made
   to move teams they did not own.
5. **Outcome** — technical result, organisational result, and
   the *delta* between the two.
6. **Failure-mode analysis** — which of the module-level failure
   modes (see [../SOLUTION.md §"Common failure modes"](../SOLUTION.md))
   this initiative encountered, and how it handled them.
7. **What you'd copy / what you wouldn't** — one paragraph each.

### Audience

Another principal engineer who is about to be asked to run a
similar initiative. Would they, after reading this, know what
to brace for?

## 2. Worked answer or implementation

Two worked examples are sketched below — one from a public
account, one illustrative. The examples are not model answers;
they are enough detail to show the shape.

### 2.1 Worked example A — gradual-typing adoption at a large Python codebase (public)

**One-paragraph summary.** A large Python-first product company
moved its multi-million-line codebase from untyped to
gradually-typed Python over multiple years, driven by a small
central team plus per-product-team champions. The effort shipped
— typed code is now the default — and the pattern has been
widely cited since.

<!-- needs-citation: specific engineer-count, calendar duration, and
     percentage-typed metrics for the Dropbox and Instagram gradual-typing
     rollouts. Public talks by Guido van Rossum and the Pyre/Pytype teams
     exist but should be cross-checked before being quoted as numbers in a
     graded case study. -->

**Scope.** Multiple product teams across an engineering org in
the low thousands. Timeline on the order of years, not quarters.
A central tools/platform team owned the type-checker and the
migration tooling; product teams owned the migration of their
own code.

**Sponsor.** CTO-level or VP-of-engineering-level sponsorship,
visible in all-hands and in engineering levelling criteria (new
code expected to be typed; refusal to type an active module
treated as a reviewable decision, not a personal preference).

**Tactics.**

1. **Make the default easy before making it mandatory.** The
   type-checker was deployed as warnings-only across the whole
   codebase for months before any team was asked to fix
   anything. Teams discovered their own highest-signal errors
   without a central mandate.
2. **Per-team champions, not central enforcement.** One named
   engineer per product team owned the local migration. The
   central team's job was to unblock champions, not to review
   PRs in product code.
3. **Tooling-first.** Auto-annotation tools and gradual-import
   support let teams adopt in slices rather than big-bang. The
   central team's hardest engineering work was the tooling, not
   the type system itself.
4. **Visible metric per team.** A per-team percentage-typed
   dashboard, updated weekly. Not a stick — a social signal.

**Outcome.** Typed-by-default is now organisational common
sense. The type-checker tooling itself was later open-sourced,
which created a secondary win: external hiring benefitted from
the public artefact.

**Failure-mode analysis.** The main failure mode that *didn't*
happen: no "we shipped but the org is bitter" outcome, because
the migration was carried in with carrots (better IDE support,
fewer production bugs in typed modules) rather than sticks. The
failure mode that *did* happen: teams with heavy
dynamic-metaprogramming code took much longer than the initial
plan assumed, and the central team under-estimated how much
custom tooling those pockets would need.

**What to copy.** The warnings-only soak period before any
mandate. The per-team champion model. The investment in tooling
before enforcement.

**What not to copy.** Treating the central team's calendar
estimate as authoritative — the long-tail code shapes consumed
more time than planned, and a case-study-aware principal would
have budgeted 1.5–2× the initial estimate from day one.

### 2.2 Worked example B — AI/ML security program rollout (illustrative)

This example is **illustrative, not a specific company**. It is
constructed to let you see what a passing case study looks like
when the public record is thin, which is the usual situation for
AI-security initiatives today.

**One-paragraph summary.** A principal engineer in the AI
platform group led a cross-org initiative to establish an
AI/ML-specific security baseline across the four product
teams that ship ML systems. The baseline tied together three
public frameworks — NIST AI RMF (`govern/map/measure/manage`)
for the control structure, OWASP ML Top 10 as the concrete
vulnerability checklist for ML training and inference, and
MITRE ATLAS as the adversarial threat-model reference. The
initiative shipped the baseline on time; team adoption split
cleanly along whether the team already had a security champion.

**Scope.** Four product teams, ~60 engineers, two quarters to
baseline + two quarters to adoption. Owners: AI platform
(central), security engineering (reviewer), four product teams.

**Sponsor.** VP of engineering, visible in two moments: (a) a
30-minute all-hands slot to introduce the baseline, and (b) a
written decision in a cross-team disagreement about model-card
requirements (see tactics below).

**Tactics.**

1. **Anchor to existing public frameworks rather than invent
   internal ones.** The baseline was presented as "our
   implementation of NIST AI RMF applied to our stack," with the
   `govern/map/measure/manage` function split used literally as
   the baseline's top-level sections. OWASP ML Top 10 and MITRE
   ATLAS were cited as the catalogues the baseline is built
   against. This made the baseline easier to defend and easier
   to audit later.
2. **Ladder of required-today vs required-next-quarter.** Each
   control was tagged either R0 (required at launch, blocking),
   R1 (required within one quarter, non-blocking), or R2
   (recommended). Teams could see the full roadmap but were
   only on the hook for R0 immediately.
3. **Security-champion-per-team, nominated by the EM.** One
   named engineer per team owned the local adoption, with 10%
   time explicitly carved out by the EM. The initiative's
   success correlated almost exactly with how strong that
   nomination was.
4. **Escalation path used exactly once.** Mid-initiative, one
   product team pushed back on the model-card requirement as
   "process overhead." Instead of negotiating indefinitely, the
   principal engineer escalated to the VP sponsor, who issued a
   written decision siding with the baseline but allowing a
   three-month deferral for that team. Using the sponsor once
   visibly made the next two conflicts resolve in meetings.

**Outcome.** R0 controls were adopted across all four teams by
end of quarter Q+2. R1 adoption split: two teams were fully on
R1 by Q+4; the other two were at ~60%, blocked on model-card
tooling the central team had promised but under-shipped. The
initiative would grade as **partial success** by its own
success criteria.

**Failure-mode analysis.** This initiative *did* hit the "equal
cost assumption" failure mode from the module SOLUTION.md:
MITRE-ATLAS-style adversarial testing was much cheaper for the
teams whose models were already behind authenticated APIs, and
much more expensive for the team shipping an open-weights model
(prompt-injection and model-extraction surfaces are larger).
The initial roadmap pretended these were equal. The partial-
success grade is directly attributable to not pricing that
asymmetry in from the start.

**What to copy.** Anchoring to NIST AI RMF, OWASP ML Top 10,
and MITRE ATLAS instead of inventing internal frameworks.
R0/R1/R2 tagging. Using the sponsor exactly once, early and
visibly.

**What not to copy.** Treating adversarial-testing cost as
uniform across model-exposure profiles. That assumption cost
the initiative an entire quarter and should have been caught in
the memo stage.

## 3. Validation steps

A finished case study passes when you can answer **yes** to all
of the following:

1. **Is the initiative real, or clearly labelled illustrative?**
   Mixing invented company names into a "real" case study is an
   automatic fail. Illustrative examples must say so in the
   first sentence of the summary.
2. **Are specific numbers either cited or marked as needing
   verification?** Any metric claim that can't be sourced to a
   public document gets `<!-- needs-citation: ... -->`. This
   blocks auto-merge in the AICG review pipeline and is the
   correct behaviour — unverifiable metrics are the most common
   way case studies drift into fiction.
3. **Does the sponsor section describe actions, not titles?**
   "VP of engineering supported it" is not sponsorship evidence.
   "VP of engineering's written decision in the Q+1
   model-card conflict is the artefact we still cite" is.
4. **Are at least three specific tactical moves named?** A case
   study with only general tactics ("they communicated well")
   does not teach the next principal engineer anything.
5. **Does the outcome section split technical from
   organisational?** Technical success with organisational
   bitterness is a specific, important outcome shape; the case
   study must be able to see it.
6. **Does the failure-mode analysis map explicitly to the
   module-level failure modes?** If none of them applied,
   explain why — not every initiative hits every failure mode,
   but "nothing went wrong" is almost never true.
7. **Would another principal engineer, reading this, know what
   to brace for?** The "what to copy / what not to copy"
   paragraphs are what makes the case study load-bearing for
   the next person.

Mechanical checks:

- Markdown lint passes (`npx markdownlint-cli2 SOLUTION.md` or
  equivalent).
- Length between ~1500 and ~3500 words. Shorter is
  underspecified; longer is unread.
- Every proper-noun company reference is either hyperlinked to
  a public source or marked illustrative.

## 4. Rubric or review checklist

Grade on a 0–3 scale per criterion. Pass threshold: ≥15/24,
with no criterion at 0.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Scope specificity | Vague | Named teams | Named teams + engineer-count estimate | Named teams, engineer-count, timeline, calendar |
| 2 | Sponsor concreteness | Title only | Title + one action | Title + specific moments | Specific moments + an artefact that still gets cited |
| 3 | Tactics named | <2 | 2 general | 3 specific | ≥3 specific + at least one that generalises |
| 4 | Outcome honesty | "Succeeded" | Succeeded + metric | Technical + organisational outcomes separated | Separated + the delta is discussed |
| 5 | Failure-mode mapping | None | One module failure mode | Two, with evidence | Three, with evidence, including one that was *avoided* |
| 6 | Verifiability | Unsourced claims | Some citations | Most claims sourced or marked | All claims sourced or marked `needs-citation` |
| 7 | Reusability | No transfer | Weak lessons | Clear "copy/don't copy" | Copy/don't-copy that another PE could act on directly |
| 8 | Writing | Hard to follow | Readable | Tight | Tight + the reader finishes it |

Reviewer notes:

- Prefer initiatives at different scales (10-engineer, 100-
  engineer, 1000-engineer) across the cohort, so cross-talk
  surfaces how tactics shift by scale.
- If the student submits an initiative they personally led,
  the rubric is the same — but the sponsor and failure-mode
  sections are usually weaker (the writer is too close to the
  work). Grade them like any other case study, and encourage
  them to interview someone from a different team who was on
  the initiative.

## 5. Common mistakes

1. **The engineering-blog paraphrase.** The student retells the
   public write-up without going through the four-axis lens.
   Fix: make the student rewrite every section against
   `scope / sponsor / tactics / outcome`, deleting any
   paragraph that doesn't fit one of those four.
2. **Sponsor = title.** "Supported by the CTO" with no action
   evidence. Fix: ask for the *artefact* — the memo the sponsor
   signed, the decision they wrote down, the meeting they
   attended.
3. **Metrics treated as facts.** Specific percentages (e.g.,
   "improved latency by 47%") are often repeated from one blog
   to the next without a primary source. Flag every such
   number with `<!-- needs-citation: ... -->` unless it is
   linked to the originating source.
4. **"It worked" without "and the org is still willing to try
   the next one."** Technical success without organisational
   success is a specific, important outcome. The case study
   must be able to see it.
5. **Failure modes glossed as "communication issues."** Almost
   every real failure maps to one of the module's named failure
   modes (underweighting sponsor, equal-cost assumption, no
   escape valves, declaring victory too early). "Communication
   issues" is usually the symptom, not the cause.
6. **No "what you'd do differently."** A case study that says
   nothing about transferable lessons is a book report, not an
   engineering artefact.
7. **Missing the mid-point grade.** Grading by the final state
   obscures the part where the initiative was most likely to
   die. A good case study identifies the mid-point and
   evaluates what pulled it through.

## 6. References

Official sources used to anchor the illustrative example in
section 2.2:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
  The `govern / map / measure / manage` function split is used
  as the structural spine of the illustrative baseline.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Cited as the concrete vulnerability catalogue the baseline
  is tested against.
- MITRE ATLAS —
  <https://atlas.mitre.org/>.
  Cited as the adversarial threat-model reference, especially
  for techniques affecting training-data integrity and model
  inference.

Internal cross-references:

- [../SOLUTION.md](../SOLUTION.md) — module-level framing,
  including the specific failure-mode list used in rubric
  criterion 5.
- [../../mod-501-technical-strategy/SOLUTION.md](../../mod-501-technical-strategy/SOLUTION.md) — the strategic-frame lens; useful when the case study's initiative was itself a strategy-level bet.
- [../../mod-502-mentorship-leadership/SOLUTION.md](../../mod-502-mentorship-leadership/SOLUTION.md) — the trust-and-relationship layer underlying the "per-team champion" tactic.

Public engineering-blog case studies that the module's
SOLUTION.md references (Dropbox typing, Instagram typing, Uber
Michelangelo, LinkedIn Pro-ML). Specific metrics from these
sources should be verified against the primary write-up before
being quoted in a graded case study; where a student quotes a
number without a primary source, mark
`<!-- needs-citation: ... -->`.
