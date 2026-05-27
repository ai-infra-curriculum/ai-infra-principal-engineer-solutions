# SOLUTION — Open Source and Community

> Read this *after* you have attempted the deliverables. The
> "solutions" are rubrics for engaging with open source as a
> principal engineer. This document explains *why* OSS engagement
> is a first-class principal-engineer concern and which engagement
> shapes pay off.

## What this module is really teaching

For most of software history, OSS was a side concern. In 2026,
that has flipped. Modern AI infrastructure is built almost
entirely on open source — PyTorch, vLLM, Kubernetes, Ray, MLflow,
LangChain, FastAPI, NumPy, pandas. A principal engineer at any
serious tech company who can't navigate the OSS world is
operating with one hand tied.

The truths the module teaches:

1. **OSS engagement is a portfolio.** Some projects you only
   consume; some you contribute fixes to; some you co-maintain;
   some you sponsor; some you release.
2. **The economics of OSS at a company scale are different from
   the economics of OSS as an individual.** Companies pay
   meaningfully for the ones they depend on, in cash or in
   engineer-time.
3. **Releasing your own OSS project is a 5-10 year commitment.**
   Most "we'll just open-source it" announcements end with a
   maintained-for-six-months ghost project.
4. **OSS reputation is a hiring channel and a recruiting risk.**
   How you behave in the OSS community is visible.
5. **Standards bodies, foundations, and trade orgs are
   underrated.** Principal engineers who engage with CNCF, MLCommons,
   etc. shape the industry's direction. Most don't.

## What the strategic deliverables should actually look like

### Case study (exercise 01): an OSS engagement story

Pick a company-OSS relationship that played out interestingly:

- **Big-tech-as-maintainer**: Meta's PyTorch, Google's
  Kubernetes. The engineering investment is enormous; the
  strategic returns are large but indirect.
- **Big-tech-extracts-value**: many companies use OSS without
  contributing back. What's the long-term consequence?
- **OSS-to-vendor pivot**: HashiCorp's BSL relicense, Elastic's
  license change, MongoDB's SSPL. Why did they do it? Was the
  community justified in their reaction?
- **Foundation governance**: how does CNCF or Linux Foundation
  actually run a project? What does that look like from the
  inside?

For each, examine:
- The **motivation** of the company engaging with the project.
- The **costs** they paid (engineer-time, license risk,
  fork risk).
- The **value** they extracted (recruiting, control,
  ecosystem).
- The **community's** response. Were they right or wrong?

Common failure modes:
- **Idealizing OSS**: "open source is just good." It's good
  *and* it's a strategic landscape. Both can be true.
- **Cynicism**: "companies just exploit OSS." Some do; many
  invest deeply.

### Strategy memo (exercise 02): the OSS strategy

A principal-engineer-level OSS strategy memo covers:

1. **Inventory**: what OSS do we depend on critically? Where
   would our company be in 6 months if these projects vanished?
2. **Engagement classification**: for each critical dependency,
   which mode are we in? (Consume / fix-and-PR / co-maintain /
   sponsor / fork.)
3. **The gap**: where are we under-engaging? Are there projects
   that carry our load but get nothing back from us?
4. **Resources**: what fraction of engineering time is
   reasonable for OSS work? (Realistic targets: 2-5% across
   the eng org for consumers; 10-25% for ML/infra orgs that
   depend heavily on OSS.)
5. **Release strategy** (if applicable): are we releasing any
   of our internal tools? Under what license? With what
   maintenance commitment?
6. **Foundation membership**: are we members of CNCF, Linux
   Foundation, MLCommons, etc.? Should we be?

The memo's most important section is **what we're choosing not
to do**: we are not going to open-source every internal tool;
we are not going to engage deeply with every project we use; we
are not going to fight the foundation politics on every
governance change. Choose battles.

Common failure modes:
- **Treating OSS engagement as charity**: it's strategic.
- **Treating OSS engagement as purely strategic**: it's also
  community membership; bad-faith engagement breaks trust that
  takes years to rebuild.
- **"We'll open-source it" without a 3-year maintenance
  commitment**: produces zombie projects that hurt the
  company's OSS reputation.

### Stakeholder mapping (exercise 03): the OSS community map

OSS stakeholders include:

- **Project maintainers** of the projects you depend on. Their
  judgment is the gating factor for many things.
- **Other big consumers** of the same projects — your peers in
  the community. Coordination with them often helps shape the
  project's direction.
- **Foundation staff and TOC members** for projects in
  foundations. They control governance.
- **Your own contributors** — the engineers in your org
  building OSS reputation. Their interests matter.
- **Internal legal / OSPO** (Open Source Program Office). They
  control what you can release.

The mapping forces a candid conversation about **influence in
the project**: how much weight does your company carry with
the maintainers? A lot (you're a major sponsor) or a little
(you've never contributed)? The honest answer determines what
you can ask for.

### Roadmap (exercise 04): the OSS engagement cadence

OSS roadmaps are longer-cycle than internal ones. The shape:

- **Quarterly**: contribution targets, foundation membership
  reviews, OSPO check-ins.
- **Annually**: which projects are we now critical-dependent
  on that we weren't last year? Which can we step back from?
- **Multi-year**: if we're releasing or co-maintaining,
  multi-year roadmap with the upstream project.

For an OSS *release*, the roadmap looks like:

1. **Year 1**: Public release, initial community, first 10
   contributors.
2. **Year 2**: First major version, first downstream production
   users, governance maturing.
3. **Year 3**: Foundation contribution discussion, first
   non-original-company maintainer.
4. **Years 4+**: Sustainable community or quiet retirement.
   Many projects die here.

### Presentation (exercise 05): the OSS pitch

Presentations on OSS strategy fall into two shapes:

- **Internal**: pitching the company on OSS investment. The
  audience cares about ROI and risk. Frame in terms of
  recruiting impact, vendor leverage, and ecosystem
  positioning.
- **External**: presenting at an OSS conference, foundation
  meeting, or community gathering. The audience cares about
  authenticity and contribution. Lead with what your team has
  built and learned; the company message is a footnote.

The same person should be able to give both presentations, but
they're different talks. Mixing them up — giving a corporate
pitch at an OSS conference or a community evangelism talk to
the CFO — loses both audiences.

## Trade-offs we deliberately accepted

### US/EU OSS-economy focus

The OSS ecosystem in 2026 is global but the major foundations,
funding sources, and license-decision-making concentrate in the
US and EU. The exercises reflect that. The principles
generalize.

### License decisions deferred to legal

The exercises mention licenses but don't deep-dive into the
choice between MIT, Apache, GPL, BSL, SSPL, etc. Those choices
are real and matter; they belong in a legal-engineering joint
session, not an architecture exercise.

### Foundation governance simplified

The actual mechanics of CNCF TOC, ASF, LF projects, etc. are
elaborate. The exercises treat foundation membership as a
strategic decision rather than walking through bylaws. The
strategic frame is the principal-engineer-relevant part.

## Common mistakes graders see

1. **Open-sourcing a tool without a maintenance commitment**:
   the company's OSS reputation suffers when projects go
   un-maintained. Don't release what you can't sustain.
2. **Free-riding silently**: depending on a project for years
   without contributing leaves you politically vulnerable when
   the project's direction changes.
3. **Hostile forking**: forking a project to escape governance
   you don't like is sometimes right; doing it carelessly
   permanently damages community standing.
4. **OSPO bypass**: releasing code without going through legal
   review creates real risk. Use the process.
5. **Confusing "we use it" with "we depend on it"**: critical
   dependencies need a different engagement model than
   incidental ones. Inventory honestly.
6. **Treating OSS as the dev community's hobby**: at the
   principal-engineer level, OSS is a strategic concern.
   Engage at that level.

## When to go beyond this module

- Contribute a real fix to a project your company depends on.
  The friction of doing this *as an employee* (legal review,
  CLA, CI integration) is the curriculum.
- Sit in on a **foundation TOC meeting** or **project steering
  committee**. The governance dynamics are not in any docs.
- Cross-reference the **principal-architect mod-602** for the
  standards-adoption frame, which is the architectural cousin
  of OSS engagement.

## Related curriculum touchpoints

- `principal-engineer/mod-501-technical-strategy` — OSS
  engagement as part of overall technical strategy.
- `principal-engineer/mod-505-long-term-technical-bets` — OSS
  bets are a specific kind of long-term bet.
- `principal-architect/mod-602-industry-standards` — the
  architectural angle on the same landscape.
- `mlops/projects/project-4-governance` — OSS license and
  governance considerations in production ML.
