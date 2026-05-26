# SOLUTION_OVERVIEW — Principal Engineer Track

> Read this *after* you have skimmed the module solutions. This file
> explains the design philosophy across the principal-engineer track
> and how the solutions in it differ from the solutions in junior /
> engineer / senior tracks.

## What this track is *not*

A principal-engineer solution is **not** an engineer solution with
more code. The deliverables in this repo intentionally contain less
runnable code than the lower tracks. The shift is from
*implementation* to *judgment*, and from *building it* to *deciding
what to build, what not to build, when, and why*.

If you came here expecting `make up && make test`, you are in the
wrong repo. The right repo is `ai-infra-engineer-solutions` or
`ai-infra-senior-engineer-solutions`.

## What this track *is*

Five disciplines a principal engineer applies daily:

| Module | Discipline |
|---|---|
| `mod-501-technical-strategy` | Defining a multi-year technical direction that survives stakeholder change. |
| `mod-502-mentorship-leadership` | Force-multiplying through engineers without becoming a manager. |
| `mod-503-cross-org-initiative` | Driving change that crosses team boundaries you don't own. |
| `mod-504-open-source-community` | Engaging upstream as a force multiplier (and not as a vanity project). |
| `mod-505-long-term-technical-bets` | Identifying, sizing, and defending bets whose payoff is 3+ years out. |

These are **practiced**, not **learned**. The solutions are
templates and worked examples, not exercises with answer keys.

## How a "solution" looks in this track

A principal-engineer solution typically contains:

- **A written analysis** of a real-shaped problem (technical strategy
  doc, ADR, RFC, brief).
- **A worked decision-making example** — not just "the answer", but
  the *reasoning* that arrived at the answer.
- **Templates and rubrics** — extractable artifacts you can reuse.
- **Anti-patterns** — what doesn't work and why people keep trying it.

The closest analog in lower tracks is the senior-engineer projects
(`project-201`-`204`), but even those are heavier on runnable code.

## How to read this track

### If you are about to become a principal engineer

Read in module order, but spend the most time in
`mod-501-technical-strategy` and `mod-505-long-term-technical-bets`.
Those two are where most new principals struggle: writing a strategy
that an org actually adopts, and defending a bet whose payoff is
beyond the current planning horizon.

### If you are already a principal engineer

Skim everything. The value here is the *rubrics* — templates for
artifacts you produce repeatedly. Steal what helps.

### If you are an engineering manager wondering "what does my
principal actually do?"

Read `mod-502-mentorship-leadership` first. It is the most direct
answer to that question and the easiest to act on. Then read
`mod-503-cross-org-initiative` — that is the one where you can
remove obstacles your principal cannot remove for themselves.

## Cross-cutting principles

### Influence through writing

Principal engineers ship through documents at least as much as
through code. Every solution in this track has a written deliverable,
because that is the shape of the work.

### Forecasting risk explicitly

Strategic decisions have asymmetric outcomes — a small chance of a
large loss often dominates. Solutions surface those tail risks
explicitly, not optimistically.

### Optionality preserved as long as possible

A good principal-engineer decision often defers a smaller decision
that *would* foreclose options. The solutions illustrate this with
worked examples.

### Credit and blame asymmetry

Principals get credit for visible wins and blame for invisible
losses. Solutions discuss the patterns that survive this asymmetry
(and the ones that don't).

## What's deliberately *not* in this repo

- **Detailed code implementations** — those live in the
  senior-engineer and engineer repos and are referenced from the
  artifacts here.
- **Vendor-specific tool selections** — strategies that pin a
  3-year direction to a specific vendor have a poor track record.
- **Universal answers** — the worked examples are *examples*. A
  principal applying them to a different context will rightly
  arrive at different decisions.

## Cross-references

| Topic | Deeper reference |
|---|---|
| Implementation depth | `senior-engineer-solutions/` |
| Org-change patterns | `team-lead-solutions/mod-704-cross-team-coordination/` |
| Architecture-level reasoning | `architect-solutions/projects/project-301/SOLUTION.md` |
| Transformation-strategy at executive scale | `senior-architect-solutions/projects/project-401-transformation-strategy/SOLUTION.md` |
| Long-arc industry positioning | `principal-architect-solutions/SOLUTION_OVERVIEW.md` |

## Time budget for the track

- **Surveyor read**: 1–2 weeks (read every module, internalize the
  templates).
- **Practitioner read**: continuous — these disciplines are
  practiced over a career, not "completed".
