# SOLUTION — Cross-Organization Initiative

> Read this *after* you have attempted the deliverables. The
> "solutions" are rubrics for running technical initiatives that
> cross team boundaries. This document explains *why* cross-org
> work is its own skill and why most principal engineers fail at
> it the first few times.

## What this module is really teaching

A cross-org technical initiative is the most failure-prone
project shape in software engineering:

- A single team can be moved by a strong technical leader and a
  decent manager.
- Multiple teams require **agreement** in addition to
  competence.
- Agreement across teams requires **trust**, which takes time
  the project doesn't have.
- And there's no manager who can simply tell the teams to do it
  — each team has its own manager, its own roadmap, and its own
  Q3 commitments.

Principal engineers are often asked to drive cross-org
initiatives precisely *because* they don't have positional
authority. The bet is that their technical credibility + judgment
can compensate for the lack of formal authority. Sometimes it
does. Often it doesn't.

The module exists to teach: what does the work look like when it
goes well, and what's the pattern when it fails?

## What the deliverables should actually look like

### Case study (exercise 01): a real cross-org initiative

Pick a known cross-org technical initiative. Good examples:

- The migration from monoliths to microservices at Amazon (or
  the reverse migration at companies like Segment).
- The introduction of strong typing across a Python codebase
  (Dropbox, Instagram).
- The unification of multiple ML platforms at large companies
  (Uber's Michelangelo, LinkedIn's Pro-ML).

For each, examine:
- **Scope** — how many teams, how many engineers?
- **Sponsor** — who at exec level supported it? What was that
  support worth?
- **Tactics** — how did the principal engineer running it
  actually drive it?
- **Outcome** — succeeded? Partial? Failed? *Why?*

Common failure modes:
- **Underweighting executive sponsorship**: nearly every
  successful cross-org initiative had a senior executive
  consistently asking about it. Cases without that almost
  always stall.
- **Confusing "we shipped" with "we succeeded"**: a migration
  that crosses the finish line technically but leaves the
  org bitter has failed politically.

### Strategy memo (exercise 02): the initiative plan

A cross-org initiative memo has a specific shape:

1. **The change** — what are we asking each team to do?
2. **The shared benefit** — what does each team get? (Not the
   org — each team specifically.)
3. **The shared cost** — what does each team give up? Time,
   roadmap items, integration debt?
4. **The asymmetric costs** — which teams pay more? Why? How
   are we compensating them?
5. **The dependency graph** — which team has to move first?
   Which can move in parallel? Which depend on whom?
6. **The escape valves** — what does a team do if they
   genuinely can't move on the proposed timeline?
7. **The decision authority** — who decides when teams
   disagree?

The seventh item is the most often-missed and the most
important. Cross-org initiatives stall when conflicts surface
and there's no clear path to resolution.

Common failure modes:
- **"All teams will adopt by Q3"** with no decision-authority
  framework: when Team A says no, the initiative dies in
  meetings.
- **Equal-cost assumptions**: every cross-org initiative pays
  asymmetrically. The memo should be honest about this.
- **No escape valves**: the inflexible plan produces hidden
  non-compliance.

### Stakeholder mapping (exercise 03): the multi-team politics

For cross-org work, the stakeholder map is **per team** plus
**central**:

- For each affected team: identify the **engineering manager**,
  **senior engineers**, and **product manager**.
- Centrally: identify the **executive sponsor**, the **VP-level
  arbiters**, and **the principal-engineer community** whose
  buy-in legitimizes the technical approach.

The map should show, per team, **where they are today** and
**where they need to be** for the initiative to succeed. The
delta is the work you'll do.

Common failure modes:
- **Treating each team's senior engineers as a monolith**: one
  senior engineer's opposition can derail a team's
  participation. Map individuals, not teams.
- **Skipping product managers**: product roadmaps don't make
  room for infrastructure migrations unless a product manager
  agrees. They have effective veto power; bring them in
  early.
- **No "what does each team need from the initiative team"
  inventory**: cross-org work is reciprocal. Each team is
  giving you something and should get something back.

### Roadmap (exercise 04): the per-team sequencing

A cross-org roadmap has to be **per team** and **synchronized**:

- Each team has its own track, with its own milestones, in its
  own quarter.
- The central program shows synchronization points — when does
  Team A's progress unblock Team B's?
- Risks per team are noted explicitly: "Team C's migration is
  at risk because they're also doing X this quarter."

The roadmap is not for engineering only. It's a **commitment
artifact** — each team's leadership has signed off on the
specific timeline. Without that, the roadmap is wishful.

Common failure modes:
- **Centralized roadmap that doesn't reflect team-level
  commitments**: the initiative team thinks teams are aligned;
  the teams know they're not.
- **No quarterly check-ins with the team-level commitments**:
  drift starts within 6-8 weeks of the initiative kickoff.
- **No "what if a team falls behind?" plan**: a team that
  misses a milestone needs a recovery path, not a blame
  session.

### Presentation (exercise 05): the multi-team review

Presenting a cross-org initiative is fundamentally different
from a single-team review:

- The audience includes representatives from every affected
  team. They are watching to see if their team's interests are
  visible.
- The presentation should **explicitly call out the
  asymmetries**: "Team A is paying more here. Here's what
  we're doing to compensate."
- The Q&A is where the politics surfaces. Be prepared for
  questions that are really demands.
- The presenter's job is to **hold the room** when there's
  disagreement — neither dismissing concerns nor capitulating
  to whichever voice is loudest.

A successful presentation often ends with the executive
sponsor explicitly endorsing the plan. That endorsement is
what teams will reference for the next year.

## Trade-offs we deliberately accepted

### Executive sponsorship assumed

The framework assumes you have at least minimal executive
sponsorship (VP-level support that costs you maybe 30
minutes/month). Cross-org work without it is possible but
very slow and very prone to failure. The exercises bias toward
the case with sponsorship.

### English-speaking corporate norms

Multi-team coordination patterns vary by culture. The rubrics
assume the relatively-direct communication style of US/UK
tech companies. Adapt for higher-context cultures (Japan,
parts of Europe) where the same outcomes require different
ceremony.

### Single-org assumption

The "org" here is one company. Cross-company technical
initiatives (standards bodies, open-source consortia) follow
similar shapes with different time scales (years vs. quarters)
and different leverage (no central executive). The module
acknowledges this but focuses on within-company.

## Common mistakes graders see

1. **Underestimating duration**: cross-org initiatives take
   2-4x longer than single-team work. A 6-month plan is
   usually a 12-18 month reality.
2. **Treating opposition as obstruction**: most opposition is
   information. The team saying "this won't work for us" has
   specific reasons. Find them out.
3. **No central program management**: a cross-org technical
   initiative needs someone who's keeping the per-team
   commitments visible and asking the awkward questions.
   That's often the principal engineer.
4. **Executive sponsor not actively used**: the sponsor's value
   is in (a) public endorsement, (b) escalation when teams
   conflict, (c) budget protection. If you're not using them,
   they're not actually sponsoring.
5. **Declaring victory too early**: a migration that's 80%
   done is often the most dangerous state — the new system
   exists, the old system exists, and nobody owns the last
   20%. Plan for the long tail.
6. **No retro after milestones**: cross-org work is hard;
   teams need to surface what's working and what isn't.
   Without retros, the next milestone is a worse version of
   the last.

## When to go beyond this module

- Lead an actual cross-org initiative end-to-end. Nothing
  substitutes for the experience.
- Sit in on a **cross-org initiative retro** at a company that
  ran one well (or poorly). The patterns are visible from the
  outside.
- Cross-reference the **principal-architect mod-604** for the
  coalition-building framework that underlies cross-org work.

## Related curriculum touchpoints

- `principal-engineer/mod-501-technical-strategy` — the
  strategic frame for an initiative.
- `principal-engineer/mod-502-mentorship-leadership` — the
  trust and relationship layer that makes cross-org work
  possible.
- `principal-architect/mod-604-stakeholder-coalition` — the
  political execution.
- `team-lead/mod-701-team-strategy` — how a team accepts
  cross-org work into its own plan.
