# SOLUTION — mod-504 exercise-03: OSS Stakeholder Map

> Read this *after* you have attempted the mapping. This is a
> template, a worked example, and a rubric — not a drop-in
> answer. A real stakeholder map is specific to a specific
> company and a specific OSS project, and is a living document.

## 1. Solution overview

### What the exercise is asking for

The stakeholder map identifies the people and organizations
whose decisions affect, and are affected by, the company's
engagement with a chosen OSS project. The map is the input to
every subsequent OSS-engagement conversation: who to talk to,
what influence we actually carry, what we can ask for, what we
cannot.

The module-level solution in [`../SOLUTION.md`](../SOLUTION.md)
names five stakeholder categories:

1. **Project maintainers** of the projects we depend on.
2. **Other big consumers** of the same projects (peer orgs).
3. **Foundation staff and TOC / PMC members** where relevant.
4. **Our own contributors** — engineers in our org with public
   contributor identities.
5. **Internal legal / OSPO** — who gate what we can release.

A sixth category, often missed, is **downstream consumers of
our use** — customers and partners who depend on our use of
the OSS. They carry influence inversely: when they ask us to
stay on a project, we cannot easily leave.

### What "passing" looks like

A reviewer should be able to answer "yes" to all of:

- Does the map name **individuals** where possible, not just
  roles? ("The PyTorch Foundation board" is weaker than named
  people in the right seats.)
- Does the map include an honest **influence** score, meaning
  the influence *the map author's org* carries with each
  stakeholder — not the stakeholder's own clout?
- Does the map distinguish **current** influence from
  **achievable** influence over the next 12 months?
- Does the map include the **internal** stakeholders (legal,
  OSPO, finance)? Many authors skip these and then are
  surprised when OSPO blocks the release.
- Does the map acknowledge **stakeholders we are hostile with
  or who are hostile with us**? Honest maps do.

## 2. Worked answer or implementation

Below is a worked example of a stakeholder map for a
hypothetical mid-sized AI-infrastructure company's engagement
with a Tier 1 dependency. The project chosen for the worked
example is a generic model-training framework (call it
*FrameworkX*) hosted in a foundation; replace names and
influence scores with specifics for a real submission.

### 2.1 The map

The map is a table, not a diagram. Diagrams look impressive
and age badly. Tables update easily and force specificity.

| # | Stakeholder | Category | Our current influence (0-5) | Achievable in 12 mo (0-5) | What they care about | What we can ask of them |
|---|---|---|---|---|---|---|
| 1 | FrameworkX lead maintainer (named individual) | Project maintainers | 1 | 2 | Project health; coherent roadmap; avoiding corporate capture | A reviewed PR per quarter; one issue triaged per month |
| 2 | FrameworkX top 10 maintainers | Project maintainers | 0 | 1 | Varies per maintainer; 3 of them work at Hyperscaler A | Issue triage, PR reviews, conference co-presentations |
| 3 | Hyperscaler A's framework-lead team | Other big consumers | 1 | 2 | Their own customers' adoption of FrameworkX; GPU vendor neutrality | Co-sponsorship of a feature; alignment on API decisions |
| 4 | Hyperscaler B's framework-lead team | Other big consumers | 2 | 3 | Our customer overlap; recruiting | Joint working group on operator stability |
| 5 | FrameworkX Foundation TSC chair | Foundation staff / TSC | 0 | 2 | Foundation reputation; funding; balanced vendor representation | Technical-steering seat eligibility; vote on charter change |
| 6 | FrameworkX Foundation executive director | Foundation staff / TSC | 1 | 2 | Membership growth; event sponsorships; meeting attendance | Visibility at the foundation's summit |
| 7 | Our senior infra engineer N1 (named) | Our own contributors | — | — | Public contributor reputation; technical growth | Allocating her 25% time commitment |
| 8 | Our ML platform lead N2 (named) | Our own contributors | — | — | Shipping internal roadmap; some doubt about OSS ROI | Owning our fix-and-PR engagement |
| 9 | Head of OSPO (named) | Internal legal / OSPO | — | — | License compliance; CLA / DCO policy; brand risk | Approving the engagement plan; gating any release |
| 10 | General Counsel (named) | Internal legal / OSPO | — | — | License risk; patent exposure; export control | Signing off on foundation membership agreements |
| 11 | Downstream enterprise customers depending on our FrameworkX use | Downstream consumers of our use | — | — | Our FrameworkX commitment persisting; migration cost if we moved | Influence the roadmap in their favor; price in a commitment |
| 12 | Competing fork maintainer (named) | Adversarial / hostile | 0 | 0 | The fork's survival; harm to FrameworkX's trajectory | Nothing; the relationship is adversarial |

### 2.2 Reading the influence scores

Influence here is **the stakeholder's willingness to take a
request from us and act on it.** Not the stakeholder's own
general importance.

- **0** — the stakeholder would not read an email from us; our
  name is unknown or associated with a prior conflict.
- **1** — they would read; they would answer with a polite
  "we'll consider it."
- **2** — they will engage on the specific technical merits of
  a proposal, but will not advocate internally for it.
- **3** — they will advocate internally for one or two of our
  requests per year that align with their interests.
- **4** — we are a routine coordination partner; they loop us
  in on decisions that affect us.
- **5** — our voice carries weight equal to a foundation-core
  stakeholder; our concern can change the roadmap.

Honest scoring is the single hardest part of the exercise.
The default new-practitioner failure mode is to overscore
current influence by 1-2 points because it feels bad to admit
we have none.

### 2.3 Reading the "achievable in 12 months" column

The 12-month column is a **planning** instrument. It tells the
strategy memo (`exercise-02`) which investments move the needle
and which do not.

Moves worth one influence point in 12 months:

- Shipping a reviewed, merged PR series on a feature the
  maintainer wanted.
- Having a named engineer attend monthly community meetings
  consistently for a year.
- Sponsoring a foundation event or hosting a community meetup.

Moves worth zero influence points in 12 months:

- Announcing we "care about" the project.
- Blog posts.
- Internal-tooling releases unrelated to the project.
- Payments without engagement (foundation dues count, but
  without attendance they carry very little).

Moves worth **negative** influence points:

- Repeatedly opening issues without PRs.
- Low-quality PRs that waste maintainer time.
- Public criticism of maintainers' decisions on hyperscaler-controlled
  communication channels.

### 2.4 The relationship graph (optional supplement)

For complex cases, supplement the table with a minimal
relationship graph showing which stakeholders influence each
other. The graph is useful for one purpose only: identifying
the **shortest influence path** to a desired outcome. If the
graph has more than ~12 nodes it has turned into a wall
diagram that nobody will consult; prune it.

### 2.5 Confidentiality handling

Stakeholder maps name individuals. They are more sensitive
than they look.

- Mark the document "Internal — Engineering Leadership" at
  minimum.
- Do not store in a shared public wiki.
- Any "adversarial" row needs a red-team review before the
  document is circulated beyond its immediate audience —
  misreading a competitor's organizational dynamics in writing
  is reputationally costly.
- Review the map every 6 months; stakeholders rotate.

## 3. Validation steps

A stakeholder map is validated against **actionability** and
**honesty**.

1. **Walk the map past one person outside the authoring team.**
   Ideally someone who has engaged with the project longer than
   the author. They will surface missing names and
   over-generous influence scores.
2. **For every "achievable in 12 months" entry, name the
   specific action that would move the score.** If there is
   no named action, lower the score to current.
3. **Verify every named individual is still in the stated
   role.** Maintainer lists change; TSC terms end. A stale name
   makes the whole map look careless.
4. **Confirm the OSPO and legal rows exist and name current
   people.** If they do not, the map is missing its gating
   constraints.
5. **Confirm at least one "adversarial" or "hostile" row is
   considered**, even if the conclusion is "no current
   adversary." Explicit absence is better than silent
   omission.
6. **Confirm the map is dated and has a review date.** 6-month
   review cadence is the default.
7. **Any claim about an individual's position or past actions
   is marked** with a `<!-- verify-source: ... -->` if it
   cannot be sourced to that person's public writing, project
   meeting minutes, or similar primary evidence.

## 4. Rubric or review checklist

Grade on a 5-point scale per row. Pass: ≥3 on every row and
≥4 on rows marked *critical*.

| # | Criterion | Scale 1-5 |
|---|---|---|
| 1 | Individuals named where possible *critical* | 1 = no names ; 3 = role names only ; 5 = named individuals in current roles |
| 2 | Influence scores are honest, including scores of 0 and 1 | 1 = everything is 3+ ; 3 = honest for externals but inflated internally ; 5 = honest, including 0s |
| 3 | Current vs. achievable influence distinguished | 1 = one column only ; 3 = both but achievable is aspirational ; 5 = both with specific actions tied to achievable |
| 4 | All five module-rubric categories covered *critical* | 1 = <3 categories ; 3 = 4 categories ; 5 = all 5 plus downstream consumers |
| 5 | Internal legal / OSPO stakeholders named *critical* | 1 = missing ; 3 = named role ; 5 = named individual with current involvement |
| 6 | At least one adversarial / hostile stakeholder considered | 1 = ignored ; 3 = handwaved ; 5 = named with honest zero-influence score |
| 7 | "What we can ask" is specific per stakeholder | 1 = missing ; 3 = vague ; 5 = specific request per stakeholder |
| 8 | Confidentiality handling named | 1 = missing ; 3 = tagged ; 5 = tagged, storage policy, review cadence |
| 9 | 6-month review date committed | 1 = missing ; 3 = mentioned ; 5 = calendar entry referenced |
| 10 | Uncertain claims marked `verify-source` | 1 = overclaims ; 3 = hedges verbally ; 5 = explicit markers |

Pass: 30 of 50 with no critical row below 3.

## 5. Common mistakes

- **Role-only mapping.** Listing "the PyTorch TOC" without
  naming individuals. Influence attaches to people, not seats.
- **Overscored current influence.** The default new-practitioner
  error. Fix by asking "when was the last time this person
  acted on a request from us," and if the answer is
  "never / don't know," the current influence is 0 or 1.
- **No internal stakeholders.** OSPO, legal, and finance are
  stakeholders. Maps that omit them are blindsided by the
  gating approvals.
- **No adversaries.** Every project has rival vendors, forking
  threats, or hostile ex-maintainers. Pretending they are not
  there does not make them go away.
- **Diagrams instead of tables.** The diagram looks impressive,
  does not get updated, and makes the influence scores illegible.
- **Treating foundation staff as homogeneous.** The executive
  director, the TOC chair, and a specific TSC member each have
  different levers. One row per person.
- **No action attached to achievable influence.** "We can get
  to 4" with no named intervention is wishful thinking.
- **One-time document.** A stakeholder map that is written once
  and never revisited is wrong within a year. Commit to a
  6-month review in the document.
- **Sharing too widely.** The map names people with influence
  scores. Treat it like a performance-review document.

## 6. References

### Local curriculum context

- [`../SOLUTION.md`](../SOLUTION.md) — the module rubric that
  names the five stakeholder categories and the "influence we
  carry" framing.
- [`../exercise-02/SOLUTION.md`](../exercise-02/SOLUTION.md) —
  the strategy memo that consumes this map and allocates
  resources against it.
- [`../../mod-503-cross-org-initiative/SOLUTION.md`](../../mod-503-cross-org-initiative/SOLUTION.md)
  — the cross-org-initiative discipline that uses the same
  influence-mapping vocabulary applied to internal stakeholders.

### Official sources named in the work packet

- NIST AI RMF — the "govern" function requires an explicit
  enumeration of stakeholders and accountabilities for
  AI-related supply-chain components, of which OSS projects
  are a subset.
  https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — reference for identifying adversarial
  stakeholders in the ML-OSS supply chain (hostile forks,
  maintainer compromise patterns).
  https://atlas.mitre.org/
- OWASP Machine Learning Security Top 10 — reference when the
  mapped project's maintainer trustworthiness is part of the
  org's ML risk surface.
  https://owasp.org/www-project-machine-learning-security-top-10/

### Non-primary context

Foundation governance documents (CNCF TOC charter, ASF board
bylaws, LF project charters) are the primary source for the
roles named in the "Foundation staff / TSC" category. A real
submission should cite the specific foundation charter for the
project being mapped, not a generic reference.

Practitioner examples: VeriSwarm's stakeholder-mapping
materials can illustrate the table shape and influence-scoring
rubric; they are not substitutes for direct observation of the
specific project's community dynamics.
