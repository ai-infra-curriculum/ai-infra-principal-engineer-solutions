# AI Infrastructure Principal Engineer — Solutions Repository

Reference solutions for [`ai-infra-principal-engineer-learning`](https://github.com/ai-infra-curriculum/ai-infra-principal-engineer-learning).

This repository is the answer key for an unusual kind of curriculum.
A principal engineer's deliverables shift from *implementation* to
*judgment* — from "build it" to "decide what to build, what not to
build, when, and why."

If you came expecting `make up && make test`, you are in the wrong
repo. The right repo is [`ai-infra-engineer-solutions`](https://github.com/ai-infra-curriculum/ai-infra-engineer-solutions)
or [`ai-infra-senior-engineer-solutions`](https://github.com/ai-infra-curriculum/ai-infra-senior-engineer-solutions).

## What's new — 2026-05-27

Module-level `SOLUTION.md` design-rationale docs for all 5 modules (mod-501 technical-strategy, mod-502 mentorship-leadership, mod-503 cross-org-initiative, mod-504 open-source-community, mod-505 long-term-technical-bets). Each doc explains how the principal-engineer lens differs from the principal-architect lens, what the strategic deliverables (case study, strategy memo, stakeholder map, roadmap, presentation) should actually look like, and where most senior engineers get it wrong the first few times. Audit score: 45 → 55.

## Track Overview

| Track Tier | Level | Repo |
|---|---|---|
| Principal Engineer | 6 (force-multiplier, multi-team) | this repo |
| Senior Engineer | 5 (system-scale technical contribution) | [`ai-infra-senior-engineer-solutions`](https://github.com/ai-infra-curriculum/ai-infra-senior-engineer-solutions) |
| Engineer | 4 (production engineering) | [`ai-infra-engineer-solutions`](https://github.com/ai-infra-curriculum/ai-infra-engineer-solutions) |

## Repository Structure

```
ai-infra-principal-engineer-solutions/
├── README.md
├── SOLUTION_OVERVIEW.md         # design philosophy across the track
├── SOLUTIONS_INDEX.md           # quick navigation
├── LEARNING_GUIDE.md
├── CURRICULUM.md
├── CONTRIBUTING.md
├── modules/
│   ├── mod-501-technical-strategy/
│   ├── mod-502-mentorship-leadership/
│   ├── mod-503-cross-org-initiative/
│   ├── mod-504-open-source-community/
│   └── mod-505-long-term-technical-bets/
├── projects/                    # capstone-level technical-leadership exercises
├── guides/
└── resources/
```

## Modules

| Module | Discipline |
|---|---|
| [mod-501-technical-strategy](modules/mod-501-technical-strategy) | Defining a multi-year technical direction that survives stakeholder change. |
| [mod-502-mentorship-leadership](modules/mod-502-mentorship-leadership) | Force-multiplying through engineers without becoming a manager. |
| [mod-503-cross-org-initiative](modules/mod-503-cross-org-initiative) | Driving change that crosses team boundaries you don't own. |
| [mod-504-open-source-community](modules/mod-504-open-source-community) | Engaging upstream as a force multiplier — and not as a vanity project. |
| [mod-505-long-term-technical-bets](modules/mod-505-long-term-technical-bets) | Identifying, sizing, and defending bets whose payoff is 3+ years out. |

Each module contains five exercise-level solutions. The shape of a
"solution" here is a technical-strategy memo, a mentorship plan, a
cross-org influence narrative, an upstream-engagement strategy, or a
long-bet investment thesis — see [`SOLUTION_OVERVIEW.md`](SOLUTION_OVERVIEW.md).

## Cross-Cutting Principles

1. **Force multiplication beats heroic output.** A principal engineer
   whose throughput is limited by their personal calendar is operating
   at staff level. The deliverables are designed to scale through
   other people.
2. **Influence without authority is the job.** Cross-org work has
   no formal line management; the artifact set here is the toolkit
   for getting it done anyway.
3. **Time horizon is the differentiator.** Senior engineers ship
   this quarter; principal engineers shape what gets shipped two
   to five years from now.

## How to Read This Repo

- **Aspiring to the role**: read in module order; pay special
  attention to `mod-503` (the cross-org muscle is the hardest to
  develop).
- **Already a principal engineer**: skim the templates; the value
  is in the anti-pattern sections from real failed cross-org
  initiatives.
- **Engineering managers**: read `mod-502` to understand what a
  good principal engineer's growth plan for ICs looks like.

See [`LEARNING_GUIDE.md`](LEARNING_GUIDE.md) for a structured
reading plan.

## Prerequisites

- [Senior Engineer track](https://github.com/ai-infra-curriculum/ai-infra-senior-engineer-learning) (recommended).
- Alternative path: [Principal Architect track](https://github.com/ai-infra-curriculum/ai-infra-principal-architect-learning) for strategic principals.

## Example Deliverables

- Multi-year technical strategy adopted by an engineering organization.
- Cross-team initiative landed without org-chart authority.
- Upstream contribution program with measurable ROI.
- Technical-bet thesis with named graduation criteria over a 3-year arc.
- Mentorship rubric used across an IC ladder.

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md). PRs welcome for new case
studies and alternative templates. Implementation-heavy PRs belong
in the engineer / senior-engineer repos instead.

## License

See [`LICENSE`](LICENSE).
