# SOLUTION — Project 05: Technical Leadership Capstone

> Read this *after* you have attempted the capstone. This is a
> rubric for a multi-deliverable artifact set, not a worked
> single answer. The capstone is where the five modules
> (strategy, mentorship, cross-org, open-source, long-term bets)
> get exercised at the same time on a single real-shaped
> problem. The point is to demonstrate **integrated technical
> leadership**, not five disconnected deliverables stapled
> together.

## 1. Solution overview

### What the capstone is asking for

The four prior capstone projects in this track each have an
engineering centre of gravity:

| Project | Centre of gravity |
|---|---|
| `project-01-distributed-training` | scaling a hard system |
| `project-02-platform-integration` | making heterogeneous systems coexist |
| `project-03-performance-optimization` | making something already-shipping faster |
| `project-04-innovation-poc` | proving a long-bet hypothesis cheaply |

`project-05-technical-leadership` has no engineering centre of
gravity. Its centre of gravity is **the principal engineer
themselves**, exercising the five disciplines on one real,
multi-quarter initiative inside a real org. The work product is
not a system — it is the artifact set that a principal engineer
would produce while leading such an initiative, plus the
written reflection on what that leadership looked like in
practice.

### What "passing" looks like

A capstone submission passes when an independent reviewer (the
target audience is another principal engineer at a different
company) can read the artifact set and answer "yes" to all of:

1. **Is this a real-shaped problem?** Not a textbook exercise.
   The problem is contested, multi-stakeholder, has a non-trivial
   technical core, and has plausible failure modes that are not
   just "the team didn't try hard enough."
2. **Did the candidate exercise judgment, not just process?**
   The reviewer can identify at least three decisions where the
   candidate chose A over B for stated reasons, and at least one
   decision the candidate now believes was wrong.
3. **Did force multiplication actually happen?** The narrative
   shows other engineers shipping work that they would not have
   shipped (or would have shipped worse) without the candidate's
   intervention. "I shipped X myself" does not satisfy this
   criterion.
4. **Were the long-term implications surfaced?** The reviewer
   can articulate the multi-year bet implicit in the initiative,
   and the reversal path if the bet doesn't pay off.
5. **Is there a credible mentorship and cross-org through-line?**
   At least one named mentee whose growth the initiative
   accelerated, and at least one named cross-org dependency the
   candidate moved without owning the org chart.

A submission that produces five strong deliverables for five
*different* initiatives fails this capstone. The integration is
the point.

### Scope and shape of the deliverable set

The capstone artifact set has six required pieces. They overlap
deliberately — the integration is visible in the overlaps.

1. **Initiative brief** (~2 pages). The contested problem, the
   chosen direction, the stakeholders, the timeline.
2. **Technical-strategy memo** (~4-6 pages). The bet, the hard
   problem in the middle, success metrics, kill criteria.
   Cross-references `mod-501`.
3. **Cross-org execution narrative** (~3-5 pages). Which teams
   touched, what changed for them, how the candidate moved work
   without authority. Cross-references `mod-503`.
4. **Mentorship and IC-growth ledger** (~2-3 pages). Named
   engineers, their growth arcs over the initiative,
   specific interventions and their results.
   Cross-references `mod-502`.
5. **Upstream / community engagement note** (~1-2 pages).
   What landed upstream, what didn't, what's deferred. If the
   initiative produced nothing upstream-worthy, an explicit
   "and here's why that was the right call." Cross-references
   `mod-504`.
6. **Reflection** (~2-3 pages). What worked, what didn't, what
   the candidate would do differently. **Required to include at
   least one decision the candidate now believes was wrong**,
   not just "things we wish had gone faster." Cross-references
   `mod-505` for the bet-evaluation lens.

Total: ~15-22 pages of prose plus any diagrams. Anything much
shorter is underspecified; anything much longer is unread.

## 2. Worked answer or implementation

This section walks through what each of the six pieces looks
like *when done well*. The illustrative initiative used below
is **"adopting a long-lived inference-cache layer as the org's
standard pattern for high-QPS model serving over the next 18
months."** It is a deliberate, realistic shape — large enough
to require strategy, cross-org coordination, mentorship, and a
long-term bet; small enough that a single principal engineer can
plausibly own the technical leadership.

### 2.1 Initiative brief

A well-formed brief answers, in this order:

1. **What contested problem are we solving?** "Three teams
   (`recsys`, `ranking`, `search`) independently built inference
   caches in the last 18 months. None of them generalize. Two of
   them are already showing operational scars. We need one
   coherent pattern, owned, before the fourth team does it
   again."
2. **What are we *not* doing?** "We are not building a generic
   ML feature store. We are not standardizing the model-serving
   runtime. We are not moving these workloads off our existing
   inference platform."
3. **Who has skin in the game?** Named teams, named tech leads,
   named EMs, named principal architect. The brief is signed by
   the candidate and counter-signed by at least the engineering
   director whose org will absorb most of the work.
4. **What is the rough budget?** Engineer-quarters per team, not
   dollars. A brief that doesn't price the work has not
   survived contact with reality.
5. **How will we know we're done?** A concrete pair of
   conditions, e.g., "by end of quarter Q+4, ≥3 production
   workloads (one per team) are serving traffic through the
   standard cache, and the on-call burden on those workloads has
   not increased relative to baseline."

The brief is a **commitment**, not a proposal. If the reader
can't tell whether the candidate is committed, the brief is
wrong.

### 2.2 Technical-strategy memo

This piece runs the `mod-501` rubric — the bet, the hard
problem, the engineering risks, the metrics, the "we'll know
by" date — applied to this specific initiative.

The *integration* signal in this memo: the bet is named
*specifically* enough that a reviewer could falsify it. Not
"caching will improve performance" — "we believe a shared
inference-cache layer will reduce p99 model-serving latency by
≥30% on the three target workloads, with no more than +5%
operational headcount on the owning team, validated by end of
quarter Q+4."

The hard problem in the middle, named explicitly: "cache
invalidation under online learning is the genuinely novel
engineering work in this initiative. We have not solved it. The
roadmap below assumes 2 engineer-quarters of focused work on
just this sub-problem."

The kill criterion, named explicitly: "if by end of Q+2 we have
not produced a cache-invalidation design that the three target
teams *and* the principal-architect of the inference platform
sign off on, we wind the initiative down and revert to a
documentation-only standardization."

### 2.3 Cross-org execution narrative

The cross-org piece (running the `mod-503` rubric) is the
single most failure-prone deliverable in this capstone. The
common failure mode is that it reads like a project status
report. The right shape is a **narrative of moves** the
candidate made, in something close to chronological order, with
the explicit observation: "I do not own these teams. Here is how
each team got moved anyway."

A worked example move:

> Quarter Q+1, week 4: `ranking` team had committed their own
> in-process cache redesign to their roadmap for the
> following quarter. Their tech lead (named) was the most
> credible technical skeptic of the shared-layer idea. I spent
> a week pair-debugging their existing cache's most painful
> outage with them. After that, I proposed: their planned
> redesign becomes the reference implementation for the
> shared layer; their tech lead becomes the technical owner
> of the cache-invalidation sub-problem. They said yes. The
> conversion of the most credible skeptic into the technical
> owner is what unblocked the rest of the initiative.

The narrative should include at least three such moves. It
should also include at least one move that **failed** and how
the candidate adapted. A perfectly successful cross-org
narrative reads as unreal.

### 2.4 Mentorship and IC-growth ledger

This piece runs the `mod-502` rubric, but tailored to "what
growth happened *because of* this initiative" rather than a
general mentorship plan.

For each named engineer (3-5 is the right number — fewer is
under-claimed force multiplication; more is dilution), the
ledger answers:

1. **Who they were at the start.** Level, scope, specific
   strengths and gaps.
2. **What role they ended up holding in this initiative.** Owner
   of the cache-invalidation sub-problem, lead reviewer of
   migration PRs, operational owner during rollout, etc.
3. **Specific moments the candidate intervened.** Not "I
   coached them" — "in the cache-invalidation design review on
   <date>, I pushed back on the proposed mark-and-sweep approach
   and asked them to spend a week prototyping a version-vector
   alternative. They came back with a better design than either
   of us would have produced alone."
4. **Where they are now.** Level transition, scope expansion,
   or — equally valid — "they are now the technical owner of
   the shared cache and stayed at the same level deliberately
   to focus on the operational maturity work."
5. **What the candidate explicitly did not do.** "I did not
   write any of the production cache-invalidation code, despite
   being the person most able to write it quickly. The deliberate
   non-action was the multiplier."

The "did not do" line is the single sharpest signal of
principal-engineer-level mentorship.

### 2.5 Upstream / community engagement note

The `mod-504` lens applied to this initiative. Honest answers
beat impressive ones.

Three valid shapes for this note:

- **Yes, we landed work upstream.** Specifically, what landed,
  in which project, who owns the relationship now. If the
  upstream work was a vanity contribution that doesn't survive
  the initiative ending, say so.
- **No, we deliberately didn't go upstream.** With reasons.
  Often the right call for an internal-pattern initiative;
  pretending it should have been upstreamed is a tell.
- **We tried and were rejected.** Far more interesting than the
  first two if it actually happened. What was the maintainer's
  objection? What did we learn about the design from the
  rejection?

For the worked example: a plausible truthful answer is "we
contributed a cache-key-construction helper to the public
`text-embeddings-inference` project; we deliberately did not
try to upstream the invalidation layer because it is too
coupled to our online-learning pipeline to generalize; we
considered open-sourcing the cache as a stand-alone library and
decided not to because the operational story isn't mature enough
to support external users."

### 2.6 Reflection

The reflection is the integration layer. It is the section a
reviewer reads first to decide whether to take the rest
seriously.

A good reflection covers, in order:

1. **The bet, restated with the benefit of hindsight.** Was the
   long-term thesis correct? What did we underestimate? What did
   we overestimate?
2. **The decision the candidate now believes was wrong.** This
   is required. The most useful version names the decision,
   names what the candidate believed at the time, names what
   information would have changed the decision, and names
   whether that information was available at the time.
3. **The decision that looked wrong and turned out right.**
   Often the more instructive one. Why was the candidate right
   in the face of credible disagreement?
4. **The force-multiplication audit.** Of the engineering output
   produced during this initiative, how much was the candidate's
   personal output and how much was other engineers' output that
   would not have happened without the candidate? If the honest
   ratio is <3:1 (others-to-self), the candidate was operating
   at staff level, not principal.
5. **The next bet.** A principal engineer at the end of one
   initiative is at the start of the next. What does the
   landscape now look like, and what is the natural next bet?

A reflection that contains no errors, no surprises, and no
visible learning is a signal the candidate is not yet operating
at this altitude.

## 3. Validation steps

The capstone has no automated validation — there is no test
suite for technical leadership. The validation is a structured
review process.

### 3.1 Self-validation checklist

Before submitting, run this against your own artifact set:

- [ ] Initiative brief signed by at least one stakeholder
      outside the candidate's reporting line.
- [ ] Strategy memo names the bet specifically enough that it
      could be falsified.
- [ ] Strategy memo includes a written kill criterion.
- [ ] Cross-org narrative names at least three specific moves
      and at least one failed move.
- [ ] Mentorship ledger names 3-5 engineers, each with a
      "what I did not do" line.
- [ ] Community note has one of the three valid shapes
      (landed / deliberately-didn't / tried-and-rejected) and
      is honest about which.
- [ ] Reflection names at least one decision the candidate now
      believes was wrong.
- [ ] Reflection contains a force-multiplication ratio with the
      candidate's honest estimate.
- [ ] Total artifact set fits in the 15-22 page envelope. If
      not, the brief and reflection should grow at the expense
      of the strategy memo's appendices, not the other way
      around.

### 3.2 Peer review

Have the artifact set read by:

1. **A principal engineer at a different company** — they will
   catch the parts that read as company-specific jargon and the
   parts where the candidate has confused company process for
   judgment.
2. **A principal architect at the candidate's own company** —
   they will catch the parts where the technical strategy
   collides with the architectural strategy, and the parts where
   the candidate has overstepped into architecture.
3. **The engineering manager(s) of the named mentees** — they
   will catch the parts where the mentorship narrative does not
   match the mentees' actual development plans, or where the
   candidate has claimed credit the manager would not endorse.
4. **At least one of the named mentees** — they will catch the
   parts where the ledger does not match their own experience of
   the interventions.

A capstone that has not survived all four reviews is
unfinished. A capstone where any reviewer says "this matches my
recollection and reads as fair" has cleared the highest bar in
this validation step.

### 3.3 The 18-month test

The deepest validation is unavailable at submission time but
should be planned for: a year and a half after the initiative
nominally ends, does the work still hold?

- Are the named mentees still operating at the level the
  ledger claims they reached?
- Is the technical bet still tracking against the metrics in
  the strategy memo, or has it quietly drifted?
- Is the cross-org coalition still intact, or did it dissolve
  the moment the initiative's deliverables shipped?
- Do the teams that absorbed the work still endorse the
  initiative, or do they now describe it as something done *to*
  them?

The capstone submission should pre-commit to an 18-month
look-back, including its scoring rubric. A principal engineer
who refuses to be evaluated against future evidence of their
work's durability is not yet operating at this altitude.

## 4. Rubric or review checklist

A reviewer scoring a capstone submission applies the rubric
below. Each row is scored 0-3: 0 = absent, 1 = present but
weak, 2 = solid, 3 = exemplary. A passing capstone scores ≥2
on every row and ≥18 total out of a possible 24.

| # | Dimension | What "3 — exemplary" looks like |
|---|---|---|
| 1 | **Real-shaped problem** | Contested, multi-stakeholder, with named credible counter-positions; not an easy win the candidate happened to be in the right place for. |
| 2 | **Bet articulated and falsifiable** | The bet is named with specific metrics and a specific kill criterion; a reviewer can imagine the world in which the bet fails. |
| 3 | **Cross-org judgment** | At least three specific moves are documented; at least one failed move is named; the moves are recognizably leadership, not coordination. |
| 4 | **Force multiplication** | Named engineers; specific interventions; explicit "what I did not do" reasoning; honest force-multiplication ratio. |
| 5 | **Long-term framing** | The 3-5 year implication of the initiative is named; the reversal path is named; the "next bet" is identified. |
| 6 | **Community / upstream realism** | The community engagement story is one of the three valid shapes and is honest about which; not aspirational. |
| 7 | **Reflection quality** | At least one named wrong decision; at least one decision-that-looked-wrong-and-was-right; clear evidence of updated mental model. |
| 8 | **Integration** | The six pieces reinforce each other; the strategy memo cites the mentorship ledger and vice versa; the reviewer can see one initiative, not six. |

### Scoring colour-code

- **18-24**: pass. The candidate is operating at principal-engineer
  level on this initiative.
- **14-17**: conditional pass. The artifact set is solid in
  some dimensions and underdeveloped in others; the reviewer
  should name the specific dimensions and ask for revision.
- **<14**: not yet. The candidate may be doing principal-level
  work in practice; the artifact set does not yet show it. The
  fix is usually in the reflection and the integration rows,
  not in the technical content.

## 5. Common mistakes

These are the failure modes graders see most often. Naming them
here so candidates can route around them.

1. **Five disconnected deliverables.** The strategy memo is
   about initiative X; the cross-org narrative is about
   initiative Y; the mentorship ledger is about people unrelated
   to either. The reviewer cannot see *one* initiative being
   led. Fix: the brief and reflection should both be visibly
   load-bearing across the other four pieces.
2. **Strategy memo without a kill criterion.** A common
   submission shape: vivid bet, vivid metrics, no answer to
   "what would make us stop?" Without a kill criterion, the
   candidate has not actually made a long-term bet — they have
   made a long-term commitment. Those are different.
3. **Cross-org narrative as project status report.** "In Q+1
   the `ranking` team adopted the standard. In Q+2 the `search`
   team adopted the standard." Status, not leadership. Fix:
   describe the *moves* — what did the candidate do, on what
   day, to convert which credible skeptic into which kind of
   ally?
4. **Mentorship by association.** "I work closely with X, Y,
   and Z, all of whom are excellent engineers." Co-location is
   not mentorship. Fix: name the specific interventions, with
   dates if possible, and name what the candidate explicitly
   did *not* do.
5. **Vanity upstream contribution.** A drive-by patch landed
   upstream during the initiative, claimed as community
   leverage in the artifact set. Fix: either name the
   contribution honestly as a one-off, or omit the section and
   explain why upstream wasn't the right venue.
6. **Reflection as victory lap.** Every decision was right;
   every milestone was hit; the team grew; the bet paid off.
   Reviewers read this as either dishonesty or
   under-development. Fix: name the wrong decision. If there
   isn't one, the candidate hasn't taken the kind of bet that
   produces principal-engineer-level learning.
7. **Force-multiplication ratio omitted or fudged.** A claim
   that "the team shipped X" without a breakdown of what the
   candidate personally shipped is a common dodge. Fix: be
   honest. If the honest answer is uncomfortable, that's
   useful data.
8. **Confusing "I led the technical work" with "I led
   technically".** The first is staff-level: the candidate was
   the most senior IC on the project. The second is
   principal-level: the candidate's *judgment*, applied through
   others, was the load-bearing input. The capstone is
   evaluating the second.
9. **No 18-month look-back commitment.** Submissions that
   refuse to be re-evaluated against future evidence are
   self-protecting. Principal engineers' work earns its
   credibility from durability; the look-back is the mechanism.
10. **Treating the project as an exam.** The capstone is not a
    one-shot test; it is the artifact set the candidate would
    have produced anyway while leading the initiative. A
    submission that reads as written-for-the-capstone fails
    the realism test. Fix: do the initiative; write up what you
    did; lightly edit for the audience.

## 6. References

This capstone deliberately does not require external citations
in the artifact set itself — the only sources that should
appear are the candidate's own initiative artifacts (the brief,
review notes, design docs, postmortems). The references below
are pointers into this same repository and to the paired
learning repository, used by the rubric and the validation
steps above.

### Within this repository

- [`SOLUTION_OVERVIEW.md`](../../SOLUTION_OVERVIEW.md) — track
  design philosophy; the "what a principal-engineer solution
  looks like" framing this capstone applies.
- [`modules/mod-501-technical-strategy/SOLUTION.md`](../../modules/mod-501-technical-strategy/SOLUTION.md)
  — the strategy-memo rubric the capstone's strategy memo runs.
- [`modules/mod-502-mentorship-leadership/SOLUTION.md`](../../modules/mod-502-mentorship-leadership/SOLUTION.md)
  — the mentorship rubric the capstone's IC-growth ledger runs.
- [`modules/mod-503-cross-org-initiative/SOLUTION.md`](../../modules/mod-503-cross-org-initiative/SOLUTION.md)
  — the cross-org rubric the capstone's execution narrative
  runs.
- [`modules/mod-504-open-source-community/SOLUTION.md`](../../modules/mod-504-open-source-community/SOLUTION.md)
  — the lens the capstone's community-engagement note applies.
- [`modules/mod-505-long-term-technical-bets/SOLUTION.md`](../../modules/mod-505-long-term-technical-bets/SOLUTION.md)
  — the bet-evaluation lens the capstone's reflection applies.

### Paired learning repository

- [`ai-infra-principal-engineer-learning`](https://github.com/ai-infra-curriculum/ai-infra-principal-engineer-learning)
  — the capstone's prompt and stakeholder scenarios are
  defined in the paired learning repository's
  `projects/project-05-technical-leadership/` directory; this
  solutions artifact is a rubric for evaluating any capstone
  submission against that prompt rather than a single worked
  answer to one fixed scenario.

### Sibling capstone projects (for cross-altitude comparison)

- [`projects/project-01-distributed-training`](../project-01-distributed-training)
  — engineering-centre-of-gravity capstone; useful contrast to
  this leadership-centre-of-gravity capstone.
- [`projects/project-04-innovation-poc`](../project-04-innovation-poc)
  — the long-bet capstone; the closest sibling and the easiest
  one to confuse with this one. The distinction: project-04 is
  about *making the bet*; project-05 is about *leading an org
  through executing on any bet, including this one*.

### Adjacent tracks

- `ai-infra-principal-architect-solutions` (when available) —
  the principal-architect-level companion to this capstone
  centres on org-wide standardization rather than initiative
  leadership; the two artifacts together describe the full
  principal-level technical-leadership surface.
- `ai-infra-senior-engineer-solutions/projects/` — the
  senior-engineer-level capstones are heavier on code and
  lighter on judgment; useful as a contrast for candidates who
  are transitioning into this track.
