# SOLUTION — mod-504 exercise-05: OSS Presentation

> Read this *after* you have attempted the presentation. This
> is a template, a worked outline, and a rubric — not a drop-in
> deck. A real presentation is tuned to the actual audience and
> is validated by being delivered to someone who will push back.

## 1. Solution overview

### What the exercise is asking for

The module-level solution in [`../SOLUTION.md`](../SOLUTION.md)
distinguishes two presentation shapes, and the exercise asks
the author to pick one and go deep rather than hybridize:

- **Internal presentation** — pitching the company on OSS
  investment. The audience is the CTO, VP Engineering, and
  finance. They care about ROI, risk, recruiting, and vendor
  leverage. They do not care about community authenticity per
  se, though they care about brand risk.
- **External presentation** — presenting at an OSS conference,
  foundation meeting, or community gathering. The audience is
  other engineers and maintainers. They care about
  authenticity, technical substance, and whether this is a
  genuine contribution or a corporate pitch.

A single principal engineer should be able to give both talks,
but **they are different talks**. Giving a corporate pitch at
a community conference or a community evangelism talk to the
CFO are the two failure modes.

### What "passing" looks like

A reviewer should be able to answer "yes" to all of:

- Was a single audience picked and the talk tuned to it?
- Does the talk have a **single central claim** that the
  speaker is defending? (Not three sub-claims. One.)
- Is there at least one slide the speaker expects the audience
  to **disagree with**, with a planned response?
- For an **internal** talk: is there a specific ask in money
  or engineer-time? Talks that end with "thank you" instead
  of "please approve X" are weaker.
- For an **external** talk: is the content **primarily
  technical**, with the company context as a footnote?
- Is the talk **under time**? A 20-minute slot is 16 minutes
  of talk plus 4 minutes of questions, not 22 minutes of
  talk and no questions.

## 2. Worked answer or implementation

Two worked outlines follow. A submission uses one, not both.

### 2.1 Worked outline — Internal talk

**Context.** Principal engineer presenting FY26 OSS strategy
to CTO, VP Engineering, Head of Finance, and the Head of OSPO.
Time: 30 minutes (22 of talk, 8 of discussion).

**Central claim.** "We should invest 15% of ML-infra
engineering time (~22 engineer-years) in upstream OSS
engagement in FY26 because the strategic cost of the gap is
larger than the engineering cost of closing it."

**Slide outline (10-12 slides).**

1. **Title / framing.** The central claim, visible.
2. **Where we are today.** The current, honest, baseline:
   percentage of eng time on OSS, named Tier 1 dependencies,
   count of contributions upstream in the last year.
3. **The cost of being invisible.** Named examples from the
   last 12 months where we needed something from a Tier 1
   project and did not have the standing to get it. Specific,
   not generic.
4. **What "closing the gap" means operationally.** Named
   engineers, named projects, 20% time allocations. Shows the
   plan has been thought through, not generated on the drive
   over.
5. **Sized cost.** 15% of ML-infra time; ~22 engineer-years in
   FY26; roughly $X in loaded cost. Shows we have run the
   numbers.
6. **Three outcomes we're buying.** Vendor leverage, recruiting
   signal, dependency risk reduction. One slide, three
   bullets, no more.
7. **What this strategy is *not* doing.** Explicit non-goals,
   from the strategy memo's §8.
8. **Risks and how we'll know if it's working.** The 12-month
   review metrics. Honest about what "not working" would look
   like.
9. **Comparables.** How peer companies invest in OSS engagement
   — *where we can cite publicly disclosed data*. If we only
   have anecdote, say so. Do not fabricate.
10. **The ask.** Numbered decisions from the strategy memo's
    §7. The slide the audience will react to.
11. **Backup: foundation-membership detail.** Only if asked.
12. **Backup: release candidates we are explicitly
    deferring.** Only if asked.

**Expected pushback and planned response.**

- *"15% is too high."* Response: show the baseline, show the
  decade-long cost of staying at the baseline, offer to phase
  (10% in H1, review at Q3 before stepping to 15%).
- *"Can't we just hire fewer and buy support?"* Response:
  support contracts don't give us commit access or influence
  on the roadmap. They buy incident response, not direction.
- *"Will any of these engineers quit to go to the foundation?"*
  Honest answer: possibly, over multi-year horizon. The deep
  OSS contributor is a different hiring and retention pool
  than the internal-only engineer; the strategy acknowledges
  this.

**Delivery notes.**
- Lead with the ask on slide 1. Repeat it on slide 10. Finance
  audiences respect ask-first framing.
- No live demos. Internal strategy talks are decision meetings,
  not product demos.
- Pre-read the strategy memo (`exercise-02`) to each principal
  attendee the day before. The meeting is to decide, not to
  read.

### 2.2 Worked outline — External talk

**Context.** Principal engineer or senior engineer giving a
30-minute talk at a project community conference or foundation
event. Audience: other engineers, maintainers, community
regulars. Time: 30 minutes (22 of talk, 8 of Q&A).

**Central claim.** "We solved [specific technical problem X]
in our deployment of [FrameworkX] by [specific technique Y],
and here is the patch series we upstreamed."

Note the shape: the claim is about **technical work our team
did and contributed back**, not about our company or our
OSS strategy. The company is the *setting*, not the subject.

**Slide outline (12-15 slides).**

1. **Title.** The technical problem. The project. The speaker.
   Company logo is small and in the corner, not the headline.
2. **The problem, concretely.** A specific failure mode we
   hit in production. Graph, trace, or screenshot.
3. **What we tried first.** The naive approach and why it
   didn't work. Technical substance, not narrative.
4. **The approach we took.** Diagrams and code. This is the
   core of the talk.
5. **Where the approach fits in the project's design.** How
   the patch relates to the project's existing abstractions.
   This is the slide that signals "we read the code before
   touching it."
6. **Results.** Measured outcomes. If we have real numbers,
   share them. If we don't, don't invent them.
7. **The upstream patch series.** PR numbers, review comments
   we got, which maintainer merged them. Credits the project,
   not us.
8. **What we got wrong.** At least one slide. Honest about
   the reviewers' pushback and how we responded. The
   authenticity signal.
9. **What this does not solve.** Explicit scope limits.
   Honest about what the audience should not expect from this
   change.
10. **How to engage.** Where the mailing list is, when the
    community call is, how to start contributing. This is
    *the project's* channels, not ours.
11. **Thanks.** Named maintainers who reviewed. A specific
    acknowledgment of the people who did the work with us.
12. **Q&A prompt slide.**

**Backup slides.**
- Detailed benchmark methodology.
- Alternatives we considered and rejected with rationale.
- The company context *if asked*.

**Expected pushback and planned response.**

- *"Why didn't you try approach Z?"* Response: real honest
  answer about what we tried; if we didn't try Z, say so and
  explain why. Do not pretend we considered everything.
- *"Is this just a corporate pitch?"* Response: "No — the
  ratio of corporate content to technical content on these
  slides is deliberate. Happy to take company-specific
  questions offline." Then actually do.
- *"How long until this is merged?"* Response: answer honestly
  with the project's cadence, not with marketing dates.

**Delivery notes.**
- Rehearse with a maintainer of the project if at all
  possible. They will catch tone issues you cannot catch
  yourself.
- If the project has a code of conduct, re-read it before
  travel. Community talks are a high-visibility test of it.
- Do not promote the company's commercial offering from
  stage. Reserve that for the booth, if there is one.

### 2.3 Hybrid: what to do if the audience is mixed

Mixed audiences (e.g., a vendor-sponsored OSS summit where
both maintainers and buyers attend) are the trap. Do not try
to serve both; pick one and expect the other to be mildly
disappointed. If the invitation explicitly requested a
business talk and the audience is technical, re-scope the
invitation if possible; if not, lean technical and address
the business framing in Q&A.

## 3. Validation steps

A presentation is validated by **rehearsal with a critic**.

1. **Rehearse the talk once end-to-end to a trusted critic**
   who is in or near the target audience. The internal talk
   rehearses with a peer principal or a friendly finance
   partner; the external talk rehearses with a project
   maintainer or long-time community member.
2. **Confirm the time budget holds.** 22 minutes of talk for
   a 30-minute slot. If the rehearsal runs long, cut
   backup-slide material into actual backup; do not cut Q&A.
3. **Confirm the central claim is visible on slide 1 and
   repeated at the end.** If it is not, the audience will not
   remember it.
4. **Confirm at least one slide is something the audience will
   disagree with**, and the speaker has a planned response. A
   talk that is universally agreeable is a talk nobody
   remembers.
5. **For external talks: confirm the technical-to-corporate
   content ratio is clearly skewed technical.** A reviewer
   should be able to flip through the slides and tell what
   the talk is "about" within 5 slides.
6. **For internal talks: confirm the ask is numbered and the
   decisions map 1:1 to the strategy memo.** If the ask
   drifts from the memo, the memo or the talk is out of
   date.
7. **Confirm no fabricated metrics.** Every number on a slide
   either traces to a cited source or carries a
   `<!-- verify-source: ... -->` comment in the speaker
   notes. If a number cannot be cited, remove it.
8. **Confirm the Q&A handling plan exists for the three or
   four questions the critic predicts.** Rehearse the
   responses out loud.

## 4. Rubric or review checklist

Grade on a 5-point scale per row. Pass: ≥3 on every row and
≥4 on rows marked *critical*.

| # | Criterion | Scale 1-5 |
|---|---|---|
| 1 | Picks one audience (internal or external) and tunes to it *critical* | 1 = mixed ; 3 = one audience, drift ; 5 = one audience, consistent |
| 2 | Single central claim visible on slide 1 *critical* | 1 = none ; 3 = buried ; 5 = visible on slide 1 and restated at end |
| 3 | Internal: specific ask in money or engineer-time *critical* | 1 = none ; 3 = vague ; 5 = numbered decisions mapping to the strategy memo |
| 4 | External: primarily technical substance *critical* | 1 = corporate pitch ; 3 = mostly technical with marketing drift ; 5 = tightly technical, company as footnote |
| 5 | At least one slide the audience will disagree with | 1 = universally agreeable ; 3 = one mild tension ; 5 = real disagreement with planned response |
| 6 | Time budget rehearsed and within slot | 1 = untimed ; 3 = rehearsed, over by >3 min ; 5 = within slot, Q&A time preserved |
| 7 | No fabricated metrics | 1 = made-up numbers ; 3 = unclear sourcing ; 5 = every number cited or flagged |
| 8 | Q&A plan for top 3-4 questions | 1 = none ; 3 = generic ; 5 = named questions with rehearsed answers |
| 9 | External: "what we got wrong" slide | 1 = absent ; 3 = token humility ; 5 = specific, substantive |
| 10 | Internal: "what this is not doing" slide (non-goals) | 1 = absent ; 3 = token ; 5 = explicit non-goals from the strategy memo |

Pass: 30 of 50 with no critical row below 3.

## 5. Common mistakes

- **Mixing internal and external framing.** The commonest
  failure. A talk that both pitches the business case and
  claims community authenticity achieves neither.
- **No single central claim.** The talk has 3-5 sub-points
  that the speaker treats as equally important. The audience
  remembers none of them.
- **Lead with context, bury the ask.** The internal talk
  spends 15 minutes on background before anyone can tell
  what is being requested. Flip: ask first, defend after.
- **Fabricated or unsourced numbers.** "40% of companies do
  X" with no citation. Either cite or remove.
- **Running over time.** 30-minute slot, 32-minute talk, no
  Q&A. The audience resents it. Rehearse until you are under.
- **Too many slides.** 12 slides for a 20-minute slot is
  usually too many. Fewer slides, denser words per slide,
  more speaker time per slide.
- **No slide about what you got wrong.** External audiences
  read this as a reliability signal. Omitting it reads as
  corporate polish.
- **Company logo on every slide.** External talks: logo
  small and once. Internal talks: no logo needed.
- **Promoting a commercial product from an OSS-community
  stage.** The fastest way to lose an entire community's
  trust.
- **Not rehearsing with a critic.** The talk sounds fine to
  the author and lands badly with the audience. The critic
  is the single highest-ROI prep step.
- **No kill / fallback for the room.** If the slide deck
  fails to load, the Wi-Fi is down, the audience is smaller
  than expected — the talk still works if the speaker knows
  the central claim and three supporting points by heart.

## 6. References

### Local curriculum context

- [`../SOLUTION.md`](../SOLUTION.md) — the module rubric that
  distinguishes the internal and external presentation shapes.
- [`../exercise-02/SOLUTION.md`](../exercise-02/SOLUTION.md) —
  the strategy memo whose §7 numbered decisions are the
  internal talk's ask slide.
- [`../exercise-04/SOLUTION.md`](../exercise-04/SOLUTION.md) —
  the roadmap whose milestones illustrate what "Year 1 of the
  plan" looks like on a slide.
- [`../../mod-502-mentorship-leadership/SOLUTION.md`](../../mod-502-mentorship-leadership/SOLUTION.md)
  — the mentorship discipline that grows the engineers who
  can give the external talks in a few years.

### Official sources named in the work packet

- NIST AI RMF — reference for the risk-framing language the
  internal talk uses when positioning OSS engagement as a
  risk-reduction investment.
  https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — reference if the talk includes an
  ML-supply-chain-security angle; cite specific ATLAS
  techniques rather than generic "AI security."
  https://atlas.mitre.org/
- OWASP Machine Learning Security Top 10 — reference if the
  talk's technical substance (external) or risk framing
  (internal) is specifically about ML components and their
  vulnerabilities.
  https://owasp.org/www-project-machine-learning-security-top-10/

### Non-primary context

For external talks, foundation code-of-conduct documents
(CNCF, LF, ASF, etc.) are the primary source for community
conduct expectations. A real submission should cite the
specific conference's code of conduct, not a generic
reference.

Practitioner implementation examples: VeriSwarm's slide
templates and rehearsal rubrics can illustrate the shape; they
are not substitutes for rehearsing with an actual critic from
the target audience.
