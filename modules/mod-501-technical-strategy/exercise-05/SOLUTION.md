# Exercise 05 — Presentation: The Engineering Review

> Companion reference for
> `lessons/mod-501-technical-strategy/exercises/exercise-05-presentation.md`.
> Read the module-level [SOLUTION](../SOLUTION.md) first — this
> is an **engineering review**, not an executive briefing.

## 1. Solution overview

The deliverable is a presentation — a slide deck plus speaker notes
— for the technical strategy from
[exercise-02](../exercise-02/SOLUTION.md). The audience is **senior
ICs and engineering leadership**, not the C-suite. That audience
choice determines everything about the shape of the deck.

The module-level SOLUTION defines the five-step shape:

1. Lead with the hard problem (≈ 5 minutes).
2. One architecture diagram, one data-flow diagram, one
   failure-mode diagram. No more.
3. The chosen approach and the two main alternatives — why we
   picked this, what we gave up.
4. Acknowledge open questions explicitly.
5. The ask: resources, commitment, a decision.

A correct deck:

- Wins the audience's respect for the problem *before* pitching
  the solution.
- Surfaces the open questions rather than hiding them.
- Ends with an unambiguous ask.

This exercise is graded against a rubric and produces a deck
(markdown slide outline + speaker notes), not code.

## 2. Implementation — worked exemplar: slide-by-slide outline

Continuing the technical bet from
[exercise-02](../exercise-02/SOLUTION.md). Each slide below is one
slide; the [speaker-note] lines are what you *say*, not what's on
the slide.

### Slide 1 — Title

> **Reconciliation-pipeline migration: engineering review**
>
> Payments platform · [author name] · [date]

[speaker-note] "This is a decision meeting. I'm asking for a yes
or a specific technical objection by the end. Agenda: hard
problem, three diagrams, alternatives, open questions, ask."

### Slide 2 — The hard problem (not the system)

Three bullets, no diagram:

- Account-balance invariant must survive moving the authoritative
  check off the synchronous write path.
- The reservation store replaces the SQL transaction as the
  correctness bottleneck.
- Compensating actions for "approved-then-invalidated" writes are
  novel for this team.

[speaker-note] "If none of this feels hard to you, stop me now —
either I've mis-scoped the problem or there's a technique I don't
know about. I want 5 minutes on this."

### Slide 3 — Why this is hard (continued)

Walk through the overdraft-check example: one paragraph on what
currently happens, one paragraph on what has to happen in the new
world, one paragraph on what goes wrong if the compensating action
lags.

[speaker-note] "This is the slide where you want to interrupt me.
Please do."

### Slide 4 — Architecture diagram (the only one)

A single diagram showing: API → reservation store → event log →
reconciliation consumer → read-model store. Mutations flow left to
right; reads flow from the reservation store during the migration
and from the read-model store at steady state.

[speaker-note] "One architecture diagram. If you're taking notes,
this is the picture of the system." Explicitly skip the deployment
topology, the Kafka cluster layout, the k8s manifests. Those are
in the design doc.

### Slide 5 — Data-flow diagram (the only one)

A single diagram of the **write path** showing the sequence:
request → reservation acquire → event emit → ack → async
materialize → optional compensate.

[speaker-note] "Second diagram. Note the ack happens at event
emit, not at materialize. That's the whole bet in one arrow."

### Slide 6 — Failure-mode diagram (the only one)

A single diagram enumerating the three failure classes and what
each one looks like at 3am:

- Reservation-store partition hot-spot → p99 reservation latency
  up, API p99 up.
- Reconciliation lag → materialization behind event log, stale
  balance in API responses.
- Compensating-action correctness bug → invariant violation in
  shadow audit.

[speaker-note] "Third and last diagram. These are the bad nights
I'm explicitly budgeting on-call attention for."

### Slide 7 — The chosen approach and two alternatives

Three columns:

| | Chosen: reservation + event log | Alt A: dual-write SQL | Alt B: sharded SQL with per-shard transactions |
|---|---|---|---|
| Correctness bet | Reservation store + compensate | Dual-write guarantees | Shard-local transactions |
| Throughput headroom | ≈ 3× | ≈ 1.5× | ≈ 2× |
| Novel work for this team | **Yes** | No | Some |
| Operational complexity | High during migration, lower at steady state | Medium always | Medium always |
| Why we did not pick it | — | Dual-write correctness is folk wisdom that doesn't hold under partition | Rearranges the problem but doesn't change the asymptote |

[speaker-note] "I spent real time on both alternatives. If you've
seen dual-write done well, come find me after — I'm not willing to
stake production correctness on it, and I'll explain why."

### Slide 8 — Open questions

Four bullets. Do not soften them.

- Can we guarantee idempotent compensation under arbitrary
  consumer-restart sequences? (Design in progress.)
- What's the correct partition key for the reservation store
  under our actual account distribution? (Benchmarking at M1.)
- Who owns the event log — payments platform or data platform?
  (Pending conversation with the data platform principal
  architect.) See
  [exercise-03](../exercise-03/SOLUTION.md).
- Finance-audit methodology for the shadow phase. (Pending
  sign-off from finance eng.)

[speaker-note] "I've said these out loud so you don't have to pull
them out of me. If you're going to raise an objection, aim it
somewhere that isn't already on this slide."

### Slide 9 — The ask

Three lines:

- **People:** 2 senior engineers + 1 SRE for 2 quarters.
- **Decision:** go/no-go on the memo today; kill-criterion review
  at end of Q1.
- **Commitment:** the roadmap in
  [exercise-04](../exercise-04/SOLUTION.md) becomes the shared
  plan of record.

[speaker-note] "I'd like a yes or a technical objection before we
leave. If you need to think overnight, I'd like the objection in
writing by EOD tomorrow."

### Slide 10 — Backup

Links: strategy memo
([exercise-02](../exercise-02/SOLUTION.md)), stakeholder map
([exercise-03](../exercise-03/SOLUTION.md)), roadmap
([exercise-04](../exercise-04/SOLUTION.md)), full design doc
(internal link).

### Format notes

- 9 content slides, 1 backup. Total deck is intentionally short.
- The deck does *not* include a problem-background slide, a
  "benefits to the business" slide, or a competitive-analysis
  slide. Those are executive-briefing shapes and would signal to
  the IC audience that the author doesn't trust them to care
  about the engineering.
- Diagrams are **one** per topic. The common failure mode here is
  five architecture slides at increasing zoom.

## 3. Validation steps

1. **Hard-problem-first test.** The first substantive slide after
   the title (= slide 2) is the hard problem, not the system
   overview and not the business case.
2. **Three-diagram test.** The deck has exactly three diagrams:
   architecture, data flow, failure modes. Anything beyond that
   is pushed to backup or to the design doc.
3. **Two-alternatives test.** The chosen approach is compared
   against at least two alternatives, with what was given up
   explicit. Single-option decks fail.
4. **Open-questions test.** There is a slide whose job is to
   surface the questions you have not yet answered. If the deck
   has no such slide, the author is hoping the audience won't
   notice. The audience will.
5. **Ask test.** The last slide names a specific decision, specific
   people, and a specific commitment.
6. **Audience-fit test.** Read every speaker note and ask "would a
   senior IC find this condescending or wasteful?" If yes, cut.
7. **Length test.** 8-12 content slides, 15-25 minutes of talk
   time. Longer says you don't trust the audience to interrupt.

## 4. Rubric

Score each dimension 0-3; passing is ≥ 10 with no dimension at 0.

| # | Dimension | 0 | 1 | 2 | 3 |
|---|-----------|---|---|---|---|
| 1 | Hard-problem framing | Not present or last. | Present but shallow. | Present, specific, early. | Present, specific, early, *and* the presenter explicitly invites interruption. |
| 2 | Diagram discipline | More than 3 diagrams, or wrong three. | 3 diagrams but at the wrong altitude. | Exactly architecture + data flow + failure modes. | Exactly the right three, each at the right altitude, each labeled with what to ignore. |
| 3 | Alternatives | Single-option. | One alternative, dismissed quickly. | Two alternatives with trade-offs named. | Two alternatives with trade-offs and at least one case where the author concedes the alternative has a genuine advantage. |
| 4 | Open questions | Hidden. | Present but softened. | Present and specific. | Present, specific, and routed to a named owner / next step. |
| 5 | Ask | Vague. | Specific but single-dimensional (people only, or decision only). | People + decision + commitment. | People + decision + commitment + a named timebox for the response. |
| 6 | Audience fit | Clearly aimed at an executive audience. | Mixed. | Aimed at senior ICs and eng leadership. | Aimed at senior ICs/eng leadership *and* signals trust in the audience (concise, no hand-holding). |

Pass thresholds:

- **< 10** — return for rewrite; most common weakness is
  dimension 6 (audience fit — the deck is secretly an executive
  briefing).
- **10-13** — accept with revisions.
- **14-16** — accept.
- **17-18** — curriculum exemplar.

## 5. Common mistakes

1. **Burying the difficulty.** Opening with a business-context
   slide instead of the hard problem. Senior ICs read this as
   "the author hasn't understood the problem."
2. **Single-option proposal.** No alternatives means the author
   either didn't consider them or isn't telling. Either way, the
   room will supply the alternatives for you.
3. **Over-detail on the easy parts.** Twenty slides on the
   standard 80% of the system and three on the novel 20%. The
   module-level
   [SOLUTION](../SOLUTION.md#presentation-exercise-05-the-engineering-review)
   flags this as a defining failure mode.
4. **Hidden open questions.** Audience sniffs them out. The deck
   has to put them on a slide; otherwise the Q&A becomes an
   interrogation rather than a decision.
5. **Executive-briefing shape.** Business-value slides, bar
   charts of estimated revenue impact, bullet lists of
   deliverables. The audience is engineers; this shape signals
   the wrong room.
6. **No ask.** The deck ends with "any questions?" instead of
   "yes or technical objection, please." An engineering review
   with no ask is a status update.
7. **Reading the slides.** Speaker notes should be what you
   *say*, which is different from what's on the slide. The
   module-level SOLUTION's "engineering review" framing implies
   the room should be interrupting you; you can't be interrupted
   if you're reading.

## 6. References

Framework references:

- NIST AI Risk Management Framework —
  <https://www.nist.gov/itl/ai-risk-management-framework>. The
  "Govern" function emphasizes transparent communication of
  risks and residual uncertainty; the "Open questions" slide is
  a direct expression of that principle.
- MITRE ATLAS — <https://atlas.mitre.org/>. If the strategy
  changes the AI-system attack surface, the failure-mode diagram
  should include at least the relevant ATLAS techniques.
- OWASP Machine Learning Security Top 10 —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Analogue for ML-specific failure modes on the failure-mode
  diagram.

Local context:

- Module-level framing on the five-step engineering-review
  shape:
  [SOLUTION](../SOLUTION.md#presentation-exercise-05-the-engineering-review).
- The strategy memo this deck presents:
  [exercise-02](../exercise-02/SOLUTION.md).
- The coalition this deck is partly addressing:
  [exercise-03](../exercise-03/SOLUTION.md).
- The roadmap referenced in the ask:
  [exercise-04](../exercise-04/SOLUTION.md).
