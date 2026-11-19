# SOLUTION — mod-504 exercise-02: OSS Strategy Memo

> Read this *after* you have attempted the memo. This is a
> template, a worked skeleton, and a rubric — not a drop-in
> answer. A real OSS strategy memo is specific to the company
> writing it and is the output of conversations with legal,
> OSPO, finance, and the engineering orgs that depend on the
> OSS stack.

## 1. Solution overview

### What the exercise is asking for

A principal-engineer-level OSS strategy memo. The audience is
engineering leadership (VP, CTO) plus the OSPO lead, with legal
and finance as secondary readers. The memo makes the
organization's OSS engagement **decidable**: an executive
should be able to read it, agree or disagree with specific
decisions, and allocate (or decline to allocate) resources.

The module-level solution in [`../SOLUTION.md`](../SOLUTION.md)
specifies six sections the memo must cover:

1. **Inventory** — what OSS the company depends on critically.
2. **Engagement classification** — consume / fix-and-PR /
   co-maintain / sponsor / fork, for each critical dependency.
3. **The gap** — where the company is under-engaging relative
   to its dependency.
4. **Resources** — the percentage of engineering time allocated
   to OSS work.
5. **Release strategy** — what the company releases itself,
   under what license, with what maintenance commitment.
6. **Foundation membership** — CNCF, LF, MLCommons, etc.

The seventh, and most important, section is **what the memo
is choosing *not* to do**. A memo that proposes to engage
with every project equally is not a strategy.

### What "passing" looks like

A reviewer should be able to answer "yes" to all of:

- Can the memo's recommendations be approved, declined, or
  modified by a specific executive sign-off step? (If "approved"
  means nothing concrete happens, the memo failed.)
- Is the inventory **honest** about critical dependencies? (If
  every dependency is "critical," the inventory is useless. If
  nothing is critical, the inventory is wrong.)
- Does the memo distinguish between **what we will do** and
  **what we will stop doing**? A strategy that only adds work
  is not a strategy.
- Is the resource ask **sized** in engineer-years or percentage
  of eng-org time? Hand-waving "more investment" does not
  count.
- Is there a **12-month review** committed to? Strategies that
  never get re-opened become zombies.

## 2. Worked answer or implementation

Below is a worked skeleton for a mid-sized AI-infrastructure
company (say, ~150 engineers, heavy reliance on ML/infra OSS).
Companies at different scales adjust the resource percentages
and foundation-membership tier accordingly.

### 2.1 Memo header

```
Memo: OSS engagement strategy, FY26
From: [Principal Engineer, Infra]
To:   CTO, VP Engineering
CC:   OSPO lead, General Counsel, Head of Finance
Date: [YYYY-MM-DD]
Status: Decision requested — approve, decline, or amend each of
        the five numbered decisions in §7.
```

The memo lives or dies on §7. Everything before §7 is to make
§7 decidable.

### 2.2 §1 — Inventory of critical OSS dependencies

Group dependencies in three tiers. The tiering is the first
real analytical act of the memo.

**Tier 1 — business-critical.** If this project vanished or
became hostile in six months, we have a material problem.
Examples that commonly belong here for an AI-infra org:

- Model training/inference frameworks (PyTorch, JAX, vLLM).
- Container orchestration (Kubernetes, containerd).
- Data pipeline / experiment tracking (Ray, MLflow, Argo).
- Core language ecosystem (NumPy, pandas, FastAPI, SQLAlchemy).

**Tier 2 — important-but-replaceable.** We use it, replacing
it would cost a quarter or more of engineering time, but the
business survives the transition.

**Tier 3 — commodity transitive.** We depend on it but through
other projects; our engagement decision is really about the
projects above it in the tree.

For each Tier 1 project, record:

- Project name and license.
- Where in the system it sits (one sentence).
- Our current engagement mode (consume / fix-and-PR /
  co-maintain / sponsor / fork).
- Who at the project knows our name (0, 1-2 people, or
  >5 people).
- Whether we have ever contributed code (yes / no / tried).
- Whether we have ever reported a security issue (yes / no).

The *"whether we have ever contributed code"* column is the
diagnostic for the gap analysis in §3.

### 2.3 §2 — Engagement classification

For each Tier 1 project, state the mode we are in **today** and
the mode we will be in **12 months from today**. The table has
five columns because change is additive: mode shifts one step
per year at most unless there's a budget decision.

| Mode | What it means operationally |
|---|---|
| Consume | We use the artifact; we don't open issues or PRs. |
| Fix-and-PR | We fix bugs that hurt us and upstream the patch. |
| Co-maintain | At least one engineer spends >20% time on the project; our name is on the maintainers list. |
| Sponsor | We pay cash to the project, foundation, or maintainer (directly or through a foundation). |
| Fork | We maintain a private or public hard fork. Treated as a cost, not a strategy. |

The Fork mode is a **flag**, not a mode we aspire to. Any
dependency currently in Fork mode is a problem to be resolved,
not a stable state.

### 2.4 §3 — The gap

For each Tier 1 project where the classification shows
*Consume* but our impact if the project went hostile is
material, name the gap and decide one of three responses:

1. **Close the gap** — commit engineering to upstream. State the
   engineer, the weekly time commitment, and the first
   measurable contribution (e.g., a reviewed PR within a
   quarter, not "engagement").
2. **Buy coverage** — pay a foundation or maintainer through
   OSPO-approved sponsorship. State the dollar amount and the
   expected relationship outcome.
3. **Accept the gap** — document explicitly that we are
   consuming without contributing, and the leadership
   acknowledges the resulting political risk.

Option 3 is a legitimate choice. The memo fails if it refuses
to allow it.

### 2.5 §4 — Resources

Name the eng-org-wide OSS time budget as a percentage of
engineer-weeks per quarter. The module SOLUTION.md's
suggested realistic ranges are:

- 2-5% for engineering orgs that consume OSS incidentally.
- 10-25% for ML/infra orgs that depend heavily on OSS.

Pick a number in that range and justify it. Convert it to
engineer-years. State the baseline for comparison — what is
the current actual spend (which is almost always lower than
the leadership's intuition suggests).

Allocate the budget by:

- **Project** — which Tier 1 projects get what share.
- **Engineer** — named engineers who will spend the time.
  Nameless time budgets evaporate.
- **Ritual** — reserve time for security-advisory triage and
  CVE response, which spikes unpredictably.

### 2.6 §5 — Release strategy

For each candidate internal tool, use the following filter in
order. A "no" to any filter means we don't release it.

1. **Ten-year maintenance commitment.** Do we have an engineer
   or team committed to maintaining this for a decade,
   regardless of org changes? If not, the tool dies six months
   after the champion leaves. Do not release.
2. **External user demand.** Is there a specific external user
   who has asked for this? "People would probably like it" is
   not demand.
3. **No competitive disadvantage.** Does releasing this tell
   competitors something about our roadmap or costs that we
   would not want them to know?
4. **License clarity.** Has legal approved the license? Has
   OSPO documented the release process? Is CLA / DCO decided?
5. **Governance plan.** Is the tool shipped with a maintainer
   roster, a code of conduct, a contribution guide, and a
   release cadence?

The default answer to "should we open-source this?" is **no**.
The memo should name the specific release candidates and the
specific "no"s that rule each one out, not list hypotheticals.

### 2.7 §6 — Foundation membership

Decide one of three for each relevant foundation (CNCF, Linux
Foundation, Apache Software Foundation, MLCommons, LFAI & Data,
others as relevant):

- **Join** — pay the membership fee; take the seat at the
  table; name the engineer who sits in which meeting.
- **Observe** — do not join, but have engineers attend open
  meetings, read public minutes, and engage in public lists.
- **Ignore** — explicitly out of scope this planning cycle;
  revisit in 12 months.

Foundation membership is often oversized as a question.
Membership fees are small relative to eng time; the real cost
is **showing up consistently**. A membership with nobody
attending the meetings is worse than no membership.

### 2.8 §7 — Decisions requested

List 3-5 numbered decisions the executive must sign off on.
Example shape:

1. **Decision 1 — resourcing.** Approve a 15% eng-time budget
   for OSS engagement in FY26, equivalent to ~22 engineer-years.
2. **Decision 2 — close two Tier 1 gaps.** Approve named
   engineer commitments to upstream PyTorch and vLLM at the
   stated allocation.
3. **Decision 3 — do not release.** Explicitly decline to
   open-source [internal tool X] on the grounds of the failed
   ten-year-maintenance filter.
4. **Decision 4 — foundation membership.** Approve joining
   the PyTorch Foundation at [tier] and MLCommons at
   [tier]; decline to join LFAI & Data this cycle.
5. **Decision 5 — review cadence.** Commit to a 12-month
   review of this strategy no later than [YYYY-MM-DD].

### 2.9 §8 — What this strategy is *not* doing

Two to five explicit non-goals. Examples:

- We are **not** proposing to open-source our internal
  training-orchestration platform. We do not have the
  maintenance commitment.
- We are **not** proposing to engage deeply with every
  Tier 2 project. We will fix-and-PR as issues arise.
- We are **not** proposing to engage with foundation
  governance debates on license-family changes. That is a
  legal-and-executive decision, not an engineering one.

## 3. Validation steps

A strategy memo is validated against **decidability**, not a
test suite.

1. **Dry-run against an executive.** Ask someone who was not
   involved in writing the memo to read §7 cold and say what
   they would sign off on and what they would not. If they
   cannot form an opinion, the memo is under-specified.
2. **Every Tier 1 dependency names a specific engineer or an
   explicit "accept the gap" decision.** Nameless commitments
   are not commitments.
3. **Resource budget converts to a specific number of
   engineer-years, not a percentage only.** Percentages hide
   the magnitude.
4. **Every "we will release X" has passed the five-filter
   check in §2.6 (ten-year maintenance, demand, competitive
   impact, license, governance).** A release recommendation
   without all five answered is a draft.
5. **Every foundation decision is one of join / observe /
   ignore with a named attendee for join.** "We should be
   involved" is not a decision.
6. **12-month review date is on the calendar** — not
   rhetorical. The memo cites the calendar entry.
7. **Unverifiable claims are marked.** Any dependency metric
   or license assertion that cannot be sourced from the
   project's own documentation or the SPDX license index is
   flagged:
   `<!-- verify-source: specific license version, SPDX
   identifier, and project governance page. -->`

## 4. Rubric or review checklist

Grade on a 5-point scale per row. Pass: ≥3 on every row and
≥4 on rows marked *critical*.

| # | Criterion | Scale 1-5 |
|---|---|---|
| 1 | Inventory distinguishes Tier 1 / 2 / 3 *critical* | 1 = no tiers ; 3 = tiers named but miscalibrated ; 5 = honest tiering with Tier 1 ≤ ~10 projects |
| 2 | Engagement mode stated for every Tier 1 project | 1 = missing ; 3 = partial ; 5 = all projects, mode today + mode in 12 months |
| 3 | Gap analysis names close / buy / accept per gap | 1 = missing ; 3 = vague "we should engage more" ; 5 = specific decision per gap |
| 4 | Resource budget in both percent and engineer-years *critical* | 1 = missing ; 3 = one or the other ; 5 = both, with baseline for comparison |
| 5 | Release strategy applies the five-filter check | 1 = missing ; 3 = applied inconsistently ; 5 = applied to each named candidate |
| 6 | Foundation decisions are join / observe / ignore with attendees | 1 = "engage" ; 3 = decision without named attendee ; 5 = decision with named attendee and cadence |
| 7 | §7 lists 3-5 numbered decisions ready for executive sign-off *critical* | 1 = no decisions ; 3 = vague decisions ; 5 = each decision is pass-or-fail testable |
| 8 | §8 lists 2-5 explicit non-goals *critical* | 1 = no non-goals ; 3 = token non-goals ; 5 = non-goals that will visibly disappoint some stakeholder |
| 9 | 12-month review date committed | 1 = missing ; 3 = "sometime next year" ; 5 = calendar entry referenced |
| 10 | Unverifiable claims marked with `verify-source` | 1 = overclaims ; 3 = hedges verbally ; 5 = explicit markers |

Pass: 30 of 50 with no critical row below 3.

## 5. Common mistakes

- **Everything is Tier 1.** The inventory names every
  dependency as critical, which means the strategy can't
  prioritize. Force tiering.
- **Resource budget is a percentage with no engineer-years.**
  "10% of eng time" sounds decisive and isn't, because nobody
  tracks it that way.
- **Release recommendations with no maintenance commitment.**
  The memo proposes to open-source a tool but doesn't name the
  engineer who will maintain it five years from now. This is
  how zombie projects are born.
- **Foundation memberships without a named attendee.** The
  memo recommends joining four foundations. Nobody attends
  any meetings. The memberships are invisible to the
  foundation and worthless to us.
- **No §8 (non-goals).** The memo only adds work, never
  subtracts. It will not survive its first resource conflict.
- **Treating sponsorship as charity.** The memo's framing is
  "give back to the community," not "secure our dependency."
  The latter is what finance will fund.
- **Ignoring legal and OSPO.** Any release recommendation that
  doesn't name the legal-review step is a draft. OSPO is not
  optional.
- **Over-specific foundation politics.** The memo spends five
  paragraphs on CNCF TOC bylaws. The CTO does not need that
  depth; refer out to a working session instead.
- **Confusing hobbyist OSS with company OSS.** The memo
  reasons about the projects as if they were individual
  volunteers. At Tier 1 scale, they are organizations with
  board members and budgets. Treat them accordingly.

## 6. References

### Local curriculum context

- [`../SOLUTION.md`](../SOLUTION.md) — the module rubric that
  defines the six required sections and the resource-budget
  ranges used in §2.5.
- [`../exercise-01/SOLUTION.md`](../exercise-01/SOLUTION.md) —
  the case-study exercise that provides the historical patterns
  (BSL relicensing, foundation moves) the strategy should learn
  from.
- [`../../mod-501-technical-strategy/SOLUTION.md`](../../mod-501-technical-strategy/SOLUTION.md)
  — the broader technical-strategy frame into which the OSS
  memo plugs.

### Official sources named in the work packet

- NIST AI Risk Management Framework — the OSS inventory and
  engagement classification above is a direct application of
  the "map, measure, manage, govern" structure from AI RMF 1.0
  to the OSS dependency surface.
  https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — reference for supply-chain-attack scenarios
  that shape how the strategy treats sponsorship versus
  engineering-engagement decisions for ML-critical OSS.
  https://atlas.mitre.org/
- OWASP Machine Learning Security Top 10 — relevant for the
  release-strategy five-filter, specifically the competitive
  and security filters when the candidate is an ML component.
  https://owasp.org/www-project-machine-learning-security-top-10/

### Non-primary context

The Business Source License, the Mozilla Public License, and
the Apache License 2.0 are referenced structurally by this
strategy template; specific memo instances must cite the
versioned license text and SPDX identifier directly.

Practitioner implementation examples: VeriSwarm's OSS
engagement playbook materials may be useful as patterns of
what fix-and-PR vs. co-maintain commitments look like
operationally. They are not substitutes for the company's own
OSPO and legal policies.
