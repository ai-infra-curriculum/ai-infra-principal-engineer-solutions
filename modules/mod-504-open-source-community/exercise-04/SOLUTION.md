# SOLUTION — mod-504 exercise-04: OSS Engagement Roadmap

> Read this *after* you have attempted the roadmap. This is a
> template, a worked example, and a rubric — not a drop-in
> answer. Real OSS roadmaps are on multi-year cycles and are
> evaluated on whether they *hold up* a year later, not whether
> they look impressive at the kickoff.

## 1. Solution overview

### What the exercise is asking for

The roadmap specifies **what happens when** across the three
cadences that the module-level solution identifies
([`../SOLUTION.md`](../SOLUTION.md)):

- **Quarterly** — contribution targets, foundation-membership
  reviews, OSPO check-ins.
- **Annually** — changes to the critical-dependency inventory,
  re-evaluation of engagement modes.
- **Multi-year** — if we are releasing or co-maintaining,
  a multi-year roadmap for the project itself.

The exercise has two flavors; a submission should pick one and
go deep rather than attempting both.

**Flavor A — Engagement roadmap.** The company is a consumer /
fix-and-PR / co-maintainer across N external projects. The
roadmap plans the next 3 years of the engagement *shape*.

**Flavor B — Release roadmap.** The company is releasing one
of its internal tools as OSS. The roadmap plans the project's
first 5 years from announcement to sustainable community (or
honest retirement).

The module-level solution supplies a stylized Year 1-4
shape for Flavor B:

- Year 1: public release, initial community, first ~10
  external contributors.
- Year 2: first major version, first downstream production
  users, governance maturing.
- Year 3: foundation-contribution discussion, first
  non-original-company maintainer.
- Year 4+: sustainable community or quiet retirement.

### What "passing" looks like

A reviewer should be able to answer "yes" to all of:

- Is there an explicit **kill criterion** at a named date?
  Roadmaps without a kill step become zombies.
- Are the quarterly milestones **falsifiable**? ("Improve
  community engagement" is not; "ship 3 reviewed PRs to
  upstream FrameworkX" is.)
- Are external dependencies acknowledged? ("This only works if
  the foundation accepts incubation" is a legitimate
  dependency; pretending it away is not.)
- Is there a **named owner** for each milestone? Nameless
  milestones do not ship.
- Does the roadmap name the **12-month review ritual** that
  will update it? Roadmaps are living documents.

## 2. Worked answer or implementation

Two worked skeletons follow. A submission uses one, not both.

### 2.1 Flavor A — Engagement roadmap (3-year horizon)

The context is the company from `exercise-02`: ~150 engineers,
heavy ML/infra OSS dependency. The roadmap plans the shape of
engagement with its Tier 1 dependencies.

#### Year 1 (FY26) — close the embarrassing gaps

Theme: stop being invisible on the projects we most depend on.

**Q1**

- Owner: Head of ML Infra.
- Milestone: Named engineers assigned 20% time to upstream
  engagement for 3 Tier 1 projects. Each engineer has a first
  PR in review by end of Q1.
- Review ritual: monthly OSPO check-in established, with agenda
  template committed to the OSPO wiki.

**Q2**

- Owner: Head of ML Infra.
- Milestone: Each of the 3 named engineers has at least 1
  merged PR upstream. Public contributor identity set up
  (company email acknowledged on project contributors list).
- Milestone: Foundation-membership decisions from
  `exercise-02` implemented — membership agreements signed,
  seats on technical steering committees applied for where
  eligible.

**Q3**

- Owner: Head of ML Infra, with OSPO.
- Milestone: First security-advisory response rehearsal (we
  are notified of a CVE in a Tier 1 dependency; measure time
  to patched internal deployment). This is a rehearsal, not a
  real incident.
- Milestone: First attendance at a project community meeting
  by each named engineer.

**Q4**

- Owner: VP Engineering.
- Milestone: 12-month engagement review. The questions the
  review must answer:
  - How many PRs merged upstream? (Target: ≥3 per assigned
    engineer.)
  - How many issues triaged? (Target: ≥10 per assigned
    engineer, upstream-visible.)
  - Did any assigned engineer become a listed maintainer? (If
    yes, report; this is a Year 2 outcome, not a Year 1
    requirement.)
  - Did any OSS engagement produce a recruiting win? (Honest
    answer: usually zero in Year 1.)
- Explicit kill / scale-down criteria: if fewer than ~2
  assigned engineers delivered their target PR volume, the
  engagement is under-resourced or mis-assigned and needs a
  named course correction, not a continuation.

#### Year 2 (FY27) — move from fix-and-PR to co-maintain on one project

Theme: pick one project where we have earned influence and
level up.

- Choose one of the three Year 1 projects for a co-maintain
  push, based on Year 1 outcomes.
- Named engineer moves to 50% time on the chosen project.
- Target: named maintainer status on the project's maintainers
  list by end of Q3.
- Target: company representation at the project's annual
  conference, speaking slot submitted and accepted.
- Explicit kill criterion: if maintainer-status PR is not
  under review by end of Q4, roll back to fix-and-PR mode and
  pick a different project for Year 3.

#### Year 3 (FY28) — foundation-level engagement

Theme: participate in governance of the project we co-maintain.

- Named engineer serves on technical steering committee or
  equivalent.
- Company sponsorship elevated to member tier appropriate for
  TOC voting (if relevant).
- Scope creep check: if the foundation engagement would require
  a second full-time engineer, revisit the resourcing decision
  explicitly rather than quietly reallocating.

### 2.2 Flavor B — Release roadmap (5-year horizon)

The context is a release candidate that has passed the
five-filter test in `exercise-02` §2.6. The project is a
generic "internal tool we are open-sourcing"; replace specifics
for a real submission.

#### Year 1 — Public release, initial community

Goals:
- Public release under Apache 2.0 (or chosen OSI-approved
  license).
- Initial external contributors: target ~10 by end of Year 1.
- First external production user.

Milestones by quarter:

**Q1**
- Owner: project champion (named).
- Milestone: public repository, README, CONTRIBUTING, CODE_OF_CONDUCT,
  LICENSE, first-year roadmap, security.md with CVE reporting
  process.
- Milestone: blog post announcing the release on the company
  engineering blog.
- Milestone: CI configured; external PRs can run tests.

**Q2**
- Milestone: 3-5 external PRs merged.
- Milestone: public issue tracker triaged within a week of
  issue filing.
- Milestone: governance document committed (how decisions are
  made, who the current maintainers are, how additional
  maintainers are added).

**Q3**
- Milestone: first external talk about the project (conference
  or community meetup).
- Milestone: downstream-user case study — at least one external
  org using in production, written up as a reference.

**Q4**
- Milestone: Year 1 retrospective published. Named external
  contributor count. Named releases cadence. Explicit
  statement of whether the project is tracking the Year 2
  plan.
- Explicit kill criterion: if the project has fewer than ~3
  external contributors or ~0 external production users by
  end of Year 1, trigger a named sustainability review —
  continue, scale down, or sunset.

#### Year 2 — First major version; governance maturing

Goals:
- 1.0 release with a documented API stability guarantee.
- 2-3 external maintainers added to the maintainers list
  (may still all be from the originating company's partner
  companies, but not all from the originating company).
- Community meetings cadence established (biweekly or monthly).

#### Year 3 — Foundation discussion; first non-originating maintainer

Goals:
- Formal discussion of whether to donate the project to a
  foundation (CNCF, Linux Foundation, Apache Software
  Foundation, LFAI & Data). Decision recorded, not deferred.
- At least one maintainer on the maintainers list is **not
  employed by the originating company**. This is the
  hardest-to-fake sustainability signal; if it hasn't happened
  by Year 3, the project is still a company project pretending
  to be OSS.

#### Year 4 — Sustainable community or honest retirement

Goals:
- Sustainable community: multi-vendor contributor base; active
  release cadence; governance that has survived at least one
  maintainer departure.
- *Or* honest retirement: the project did not reach
  sustainability; announce end-of-life, archive the repo,
  point users at an alternative. Zombie projects are worse
  than archived projects.

#### Year 5+ — Stewardship

If sustainable: the originating company's role shifts from
primary maintainer to one voice among many. The originating
company's roadmap no longer dictates the project's. This is
the goal.

### 2.3 Dependency callouts

Every roadmap has external dependencies. Name them.

- **Legal and OSPO sign-off at every release milestone.** If
  OSPO is a bottleneck, the roadmap must schedule that
  bandwidth, not pretend it's instantaneous.
- **Foundation calendars.** CNCF sandbox-to-incubation
  reviews, LF project acceptance meetings, etc. operate on
  their own schedules. The roadmap must align with those.
- **Security-advisory load.** Any Tier 1 dependency can produce
  an emergency patching cycle that eats a quarter. Reserve
  capacity.
- **Personnel churn.** Any named engineer in the roadmap can
  leave or change teams. The roadmap must survive the loss of
  any one named person; the review ritual catches this.

### 2.4 Review ritual

- **Monthly OSPO check-in** — reports progress on current
  quarter's milestones.
- **Quarterly engineering-leadership review** — adjusts
  milestones based on actual progress.
- **Annual re-planning** — rewrites the next year of the
  roadmap based on the retrospective. The 3-year and 5-year
  horizons are rewritten yearly; they are directional, not
  precise commitments.

## 3. Validation steps

A roadmap is validated against **holdability** and **honesty**.

1. **Every milestone names an owner.** Nameless milestones are
   aspirations.
2. **Every milestone is falsifiable.** A reviewer can look at
   the milestone 90 days later and answer yes or no on whether
   it was hit.
3. **Every year has an explicit kill / scale-down criterion.**
   Roadmaps that never consider failure become zombies.
4. **External dependencies are named**, not papered over.
5. **The review ritual is on the calendar.** The roadmap cites
   the specific recurring meetings that will update it.
6. **The 12-month retrospective compares outcomes against the
   Year 1 plan** — not against a revised plan written with
   hindsight. Honest retrospectives are rarer than they sound.
7. **Any claim about contributor counts or community metrics
   is marked** if it cannot be sourced to project telemetry:
   `<!-- verify-source: specific maintainer list, release
   cadence, external-contributor counts; cite project
   statistics page. -->`

## 4. Rubric or review checklist

Grade on a 5-point scale per row. Pass: ≥3 on every row and
≥4 on rows marked *critical*.

| # | Criterion | Scale 1-5 |
|---|---|---|
| 1 | Picks one flavor (engagement or release) and goes deep *critical* | 1 = mixed ; 3 = one flavor, shallow ; 5 = one flavor, deep |
| 2 | Named owner per milestone *critical* | 1 = none ; 3 = some milestones nameless ; 5 = every milestone has an owner |
| 3 | Falsifiable milestones *critical* | 1 = aspirational verbs ; 3 = mixed ; 5 = every milestone is yes/no checkable in 90 days |
| 4 | Explicit kill / scale-down criterion per year *critical* | 1 = none ; 3 = one year has one ; 5 = each year has a named kill criterion |
| 5 | External dependencies named (OSPO, foundation, security load) | 1 = ignored ; 3 = mentioned ; 5 = scheduled against |
| 6 | Review ritual with cadence | 1 = none ; 3 = vague "we'll check in" ; 5 = monthly/quarterly/annual all specified |
| 7 | Flavor-B only: Year 3 non-company maintainer target | 1 = absent ; 3 = present but soft ; 5 = present, hard, with kill criterion |
| 8 | Flavor-A only: engagement-mode transitions per year | 1 = static engagement ; 3 = one transition ; 5 = explicit transitions tied to prior-year outcomes |
| 9 | Resource reservations for security-advisory response | 1 = ignored ; 3 = mentioned ; 5 = reserved capacity named |
| 10 | Uncertain metrics marked `verify-source` | 1 = overclaims ; 3 = verbal hedges ; 5 = explicit markers |

Pass: 30 of 50 with no critical row below 3.

## 5. Common mistakes

- **No kill criterion.** The roadmap can only "succeed" — no
  path lets the project scale down or retire. This is how
  zombie projects are born.
- **Aspirational verbs.** "Grow the community," "improve
  engagement," "strengthen partnerships." Replace with
  countable outcomes.
- **Nameless milestones.** Nobody owns it, so nobody misses
  it when it slips.
- **Year 1 heroism, Years 2-5 vagueness.** The author plans
  Year 1 in detail and handwaves the rest. Year 1 is the
  easiest year to plan; the roadmap earns its keep on Years
  2+.
- **Pretending Year 1 contributor targets are "the community
  succeeding."** Ten external contributors in Year 1 is a
  signal, not success. Success is measured by whether the
  project survives the originating company's attention shift.
- **Treating foundation donation as a Year 1 milestone.**
  Foundations do not accept Year 1 projects for a reason.
  Plan foundation conversations around Year 3, not Year 1.
- **Ignoring the OSPO and legal bandwidth constraint.** The
  roadmap schedules three releases per quarter and OSPO can
  review one per quarter. Guess what happens.
- **Not scheduling security-advisory capacity.** The emergency
  patching cycle for a Tier 1 dependency will land at least
  once per year. If the roadmap has no slack, it will slip.
- **Skipping the Year 3 non-company maintainer target
  (Flavor B).** This is the single most important
  sustainability signal and the one most frequently missed.
- **Mixing Flavor A and Flavor B** — producing a vague
  document that is neither an engagement plan nor a release
  plan.
- **One-time document.** Roadmaps rewritten yearly stay
  useful; roadmaps committed once and never revisited are
  irrelevant by Year 2.

## 6. References

### Local curriculum context

- [`../SOLUTION.md`](../SOLUTION.md) — the module rubric that
  specifies the three cadences (quarterly, annual, multi-year)
  and the Year 1-4 release-project shape.
- [`../exercise-02/SOLUTION.md`](../exercise-02/SOLUTION.md) —
  the strategy memo whose decisions feed Year 1 of this
  roadmap.
- [`../exercise-03/SOLUTION.md`](../exercise-03/SOLUTION.md) —
  the stakeholder map whose "achievable in 12 months" column
  turns into Year 1 engagement milestones.
- [`../../mod-505-long-term-technical-bets/SOLUTION.md`](../../mod-505-long-term-technical-bets/SOLUTION.md)
  — the long-term-bet discipline that treats OSS releases as
  specific instances of multi-year bets.

### Official sources named in the work packet

- NIST AI RMF — the "manage" function requires explicit
  monitoring and update mechanisms for AI-relevant
  dependencies, which is the review-ritual section's
  framework.
  https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — reference for the security-advisory capacity
  reservation; adversarial ML tactics shape how emergency
  patching budget is sized.
  https://atlas.mitre.org/
- OWASP Machine Learning Security Top 10 — reference for
  release-roadmap security milestones when the project is an
  ML component (SECURITY.md, CVE process, supply-chain
  signing).
  https://owasp.org/www-project-machine-learning-security-top-10/

### Non-primary context

CNCF project lifecycle documentation (sandbox / incubating /
graduated) and Apache Software Foundation incubator
documentation are the primary sources for Year 3 foundation-
donation planning. A real submission names the specific
foundation and cites its lifecycle doc directly.

Practitioner implementation examples: VeriSwarm's roadmap
templates can illustrate the quarter-by-quarter breakdown and
the review-ritual structure. They are not substitutes for
project-specific planning with OSPO and legal.
