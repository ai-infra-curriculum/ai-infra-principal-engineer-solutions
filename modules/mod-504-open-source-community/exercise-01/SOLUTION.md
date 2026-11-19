# SOLUTION — mod-504 exercise-01: OSS Case Study

> Read this *after* you have attempted the exercise. This is a
> rubric and worked-example skeleton for a strategic case study,
> not a drop-in answer. The learning target is **judgment**:
> whether you can look at a real company-OSS relationship, name
> the forces at play, and argue a defensible interpretation.

## 1. Solution overview

### What the exercise is asking for

The case study asks you to pick one real, well-documented
company-OSS relationship and analyze it along four axes:

1. **Motivation** — why the company engaged with the project in
   that way.
2. **Costs** — engineer-time, license risk, fork risk,
   reputational risk.
3. **Value** — recruiting, control, ecosystem positioning,
   vendor-leverage reduction.
4. **Community response** — whether the community's reaction was
   justified, and what it implies about the next engagement of
   the same shape.

The module-level discussion in
[../SOLUTION.md](../SOLUTION.md) lists four archetypes to pick
from: *big-tech-as-maintainer*, *big-tech-extracts-value*,
*OSS-to-vendor pivot*, and *foundation governance*. A passing
submission picks one archetype and analyzes a specific instance
with enough fidelity that a reader who knows the project
recognizes the dynamics.

### What "passing" looks like

A reviewer should be able to answer "yes" to all of:

- Did the author pick a **real, named** situation (not a
  composite) and ground it in publicly documented events?
- Did the author distinguish between the **company's** interests
  and the **project community's** interests, rather than
  collapsing them?
- Did the author name at least one **cost** the company paid
  (not just the value they got)?
- Did the author take a **position** on whether the community's
  reaction was justified, and defend it?
- Did the author extract a **principle** that generalizes to a
  different company-OSS relationship?

A submission that reads like a Wikipedia summary of the project
fails. The point is the analysis, not the chronology.

## 2. Worked answer or implementation

Below is a worked skeleton for the *OSS-to-vendor pivot*
archetype, using HashiCorp's 2023 relicensing of its core
products from MPL 2.0 to the Business Source License (BSL) as
the subject. Three other worked skeletons (one per remaining
archetype) follow in condensed form — a submission needs **one**
complete case study, not four.

### 2.1 Worked case: HashiCorp's 2023 BSL relicense

#### Facts the analysis rests on

- HashiCorp announced in August 2023 that it was moving Terraform,
  Vault, Consul, Nomad, Packer, Boundary, and Waypoint from the
  Mozilla Public License (MPL) 2.0 to the Business Source License
  (BSL) 1.1. Source: HashiCorp's own announcement on their
  company blog.
- The BSL permits non-production use and converts to a more
  permissive license (in this case, MPL 2.0) after a change date.
  Source: the BSL text itself, maintained by MariaDB Corporation.
- Within weeks, a group of consultancies and ex-HashiCorp
  contributors announced **OpenTofu** (originally OpenTF), a
  fork of Terraform under MPL 2.0, which was later accepted into
  the Linux Foundation. Source: the OpenTofu project
  announcement and the Linux Foundation's project page.

<!-- verify-source: specific adoption/download metrics for
OpenTofu vs. Terraform in the 12 months post-fork; cite official
project telemetry rather than third-party analyst estimates. -->

#### Motivation (the company's view)

HashiCorp's stated motivation was that cloud-service companies
were repackaging HashiCorp's open-source projects as managed
services and competing with HashiCorp Cloud Platform without
contributing commensurate engineering back. From the company's
point of view, this is the familiar
*permissively-licensed-infrastructure-vs-hyperscaler-managed-service*
economic pattern that also drove the Elastic and MongoDB
relicensing decisions in prior years.

An analysis should resist two temptations:

- **Reading motivation from outcomes.** Whether or not the
  relicense "worked" commercially is a separate question from
  whether the stated motivation was genuine. Both can be true;
  neither forces the other.
- **Assuming the board, the CEO, and the engineering org all
  share one motivation.** Public companies relicense in response
  to investor pressure as well as competitive pressure. The
  memo should be honest that we are inferring, not reading
  minds.

#### Costs HashiCorp paid

A credible analysis names at least three costs:

1. **Permanent loss of community trust that MPL would persist.**
   Future relicensing is a now-visible option, which changes how
   every downstream user evaluates risk.
2. **The fork.** OpenTofu exists; a share of contributor
   mindshare and some enterprise users are now not on HashiCorp's
   roadmap.
3. **Hiring friction in the OSS-contributor labor pool.** Some
   senior infrastructure engineers will decline HashiCorp offers
   over the relicense.

The analysis should distinguish **paid-once costs** (the
announcement reaction) from **paid-continuously costs** (the
trust discount on every future community interaction).

#### Value HashiCorp extracted

A credible analysis names at least two:

1. **Blocked the managed-service substitute** for the covered
   products — the BSL explicitly carves out competing commercial
   offerings from the "use freely" permission.
2. **Provided a pricing signal** to investors and large
   customers that HashiCorp was defending its commercial moat.

The analysis should be honest that **recruiting-brand damage**
and **blocked substitutes** are both real, and the net value
depends on a 5-10 year judgment that nobody can confirm from
inside year 1.

#### Community response

- A significant contingent of the community treated the
  relicense as a breach of the prior implicit contract.
- A roughly equal contingent treated it as a reasonable
  commercial response to free-riding by hyperscalers.
- The practical outcome — OpenTofu being accepted into the Linux
  Foundation, continuing Terraform work under MPL 2.0 — suggests
  the community had the technical and organizational capacity
  to fork, which is not true of every project.

Was the reaction justified? A defensible position: **yes in the
limited sense that forking a permissively-licensed project is
exactly what the license permits and anticipates**, and **no in
the broader sense that the reaction sometimes conflated license
change with ethical bad faith**. A strong answer takes a
position and defends it rather than hedging.

#### Generalizable principle

Permissively-licensed infrastructure companies whose business
model is a managed service face an **asymmetric competitive
risk**: hyperscalers can build managed-service substitutes
cheaper, faster, and at higher margin than the original
company. BSL-style relicensing is one response. It is not
costless, and it is not the only response. A principal engineer
reading this case should be able to name at least two
alternatives (dual-licensing with a commercial feature set;
staying permissive and competing on managed-service UX;
foundation-donating the OSS and running the vendor as a pure
services play).

### 2.2 Condensed skeletons for the other three archetypes

#### Big-tech-as-maintainer — PyTorch at Meta / PyTorch Foundation

Facts the analysis rests on:
- Meta (then Facebook) released PyTorch under a permissive
  license in 2016. Source: Meta's own research blog.
- In September 2022, Meta announced the PyTorch Foundation
  under the Linux Foundation, moving PyTorch out of Meta's
  sole governance. Source: the Linux Foundation's and
  PyTorch's own announcements.
- The PyTorch Foundation's membership includes multiple
  hyperscalers and GPU vendors as of late 2022.
  <!-- verify-source: current PyTorch Foundation board and
  general member roster; cite the Foundation's member page as
  of the submission date. -->

Analysis questions:
- **Motivation:** Why donate governance of a project you built?
  Answers to consider: ecosystem credibility, hyperscaler
  neutrality so other clouds integrate without strategic
  hesitation, recruiting, and reducing vendor-lock concerns
  for enterprise adoption.
- **Costs paid by Meta:** a decade-plus of engineer-time;
  ongoing maintenance share; loss of unilateral roadmap
  control.
- **Value extracted:** PyTorch as the de facto research
  framework; recruiting; soft leverage on the industry's model
  interchange standards.
- **Community response:** generally positive, because the
  foundation move was a *reduction* in corporate control.

#### Big-tech-extracts-value — cloud vendors building managed services on permissively-licensed OSS

This is the shape that drives the Elastic / MongoDB / HashiCorp
relicensing cluster. The analysis is more honest if framed
general-case rather than attacking one specific cloud vendor by
name, because the pattern is structural.

Analysis questions to answer:
- Who is **the user** of this arrangement, and are they better
  off than they would be without the managed service?
- Does the originating project receive **engineering
  contributions back** proportional to what the managed service
  earns from it?
- What are the **governance tools** (foundations, trademarks,
  license choice) that shift the balance?

#### Foundation governance — CNCF or Apache Software Foundation project lifecycle

Pick one project and walk through its sandbox → incubating →
graduated lifecycle (CNCF) or its incubator → top-level-project
lifecycle (ASF). The analytical move: show what the governance
step actually *forces* the project to do differently (code of
conduct, trademark transfer, multi-vendor contributors, release
discipline), not just that the badge changed color.

## 3. Validation steps

A case study is validated against **evidence quality**, not
against a test suite.

1. **Every factual claim traces to an official source.** The
   company's own announcement, the foundation's own project
   page, the license text itself. Analyst estimates, blog
   commentary, and social-media reactions are secondary and
   should be labeled as such.
2. **Dates are specific to the month or quarter**, not "a few
   years ago." Reviewers should be able to open a browser and
   confirm the event occurred when the submission says it did.
3. **Any unverifiable metric is marked** with a
   `<!-- verify-source: ... -->` HTML comment that names the
   specific source that would resolve it. Auto-merge is blocked
   until the marker is resolved or the claim is removed.
4. **The community-response section quotes or paraphrases at
   least two different stakeholder positions.** If the only
   position shown is one the author agrees with, the analysis
   is weak.
5. **The "generalizable principle" survives substitution.**
   Swap the company name for a different company facing the
   same forces; the principle should still make sense. If it
   collapses, it was a story about one company, not a
   principle.
6. **Length discipline:** the finished case study is
   2000-3500 words. Shorter means the analysis didn't land;
   longer usually means chronology crowded out judgment.

## 4. Rubric or review checklist

Grade on a 5-point scale per row. A passing submission is
≥3 on every row and ≥4 on rows marked *critical*.

| # | Criterion | Scale 1-5 |
|---|---|---|
| 1 | Specific, real, named case (not a composite) *critical* | 1 = fictional ; 3 = real but under-specified ; 5 = specific instance with named dates and sources |
| 2 | Facts traceable to official sources *critical* | 1 = no sources ; 3 = mixed primary/secondary ; 5 = every non-obvious fact cites an official primary source |
| 3 | Motivation analyzed from company's view *and* community view | 1 = one view only ; 3 = both views, one thin ; 5 = both views, each defensible on its own |
| 4 | At least three distinct costs the company paid | 1 = none ; 3 = one or two ; 5 = three or more, with the paid-once vs. paid-continuously distinction |
| 5 | At least two distinct values the company extracted | 1 = none ; 3 = one ; 5 = two or more with honest hedging on net value |
| 6 | Takes a position on community response *critical* | 1 = no position ; 3 = position without defense ; 5 = position with defense that engages the counter-argument |
| 7 | Extracts a generalizable principle *critical* | 1 = none ; 3 = restates the case ; 5 = principle that survives swapping the company name |
| 8 | Any uncertain claim marked `verify-source` | 1 = overclaims ; 3 = hedges verbally but doesn't flag ; 5 = explicit markers that would block auto-merge |

A perfect score is 40. A pass is 24 with no critical row below 3.

## 5. Common mistakes

- **Wikipedia-mode:** retelling the project's history in
  chronological order with no analytical move. The question is
  "what does this teach us," not "what happened."
- **One-sided morality:** treating the company as straightforwardly
  virtuous (they released it, so they're good) or straightforwardly
  villainous (they relicensed, so they're bad). Real cases have
  both faces.
- **Confusing license with license-choice:** the interesting
  analytical work is on the *choice* between license families,
  not on the mechanics of any one license. If the memo spends
  four paragraphs explaining what the BSL is, it's spending
  words in the wrong place.
- **Hindsight framing:** "they should have done X" written in
  2026 about a decision made in 2018 is not analysis. The
  question is what information the decision-maker had at the
  time and whether the decision was defensible given that
  information.
- **Confusing "the community reacted loudly" with "the
  community was unified":** OSS communities are rarely unified.
  An honest analysis quotes at least two positions.
- **Under-sourcing:** citing "I heard on Hacker News" or an
  analyst report instead of the company's own announcement.
  The company's announcement is the primary source for its
  own motivation claims, even when you disbelieve them.
- **Scope creep:** trying to analyze the OSS industry rather
  than one case. Pick one case, do it well.

## 6. References

### Local curriculum context

- [`../SOLUTION.md`](../SOLUTION.md) — the module-level solution
  rubric, which names the four archetypes and the failure modes
  this exercise tests.
- [`../../mod-501-technical-strategy/SOLUTION.md`](../../mod-501-technical-strategy/SOLUTION.md)
  — strategy framing that the case-study analysis plugs into.

### Official sources named in the work packet

- NIST AI Risk Management Framework (AI RMF 1.0) and its
  companion documents — the risk-framing vocabulary used to
  label license, fork, and governance risks in OSS engagement.
  https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — adversarial ML tactics and techniques catalog,
  relevant when the case concerns ML-infrastructure OSS whose
  supply-chain integrity is in scope.
  https://atlas.mitre.org/
- OWASP Machine Learning Security Top 10 — reference when the
  case study's OSS project is an ML component whose adoption
  carries security risk the consuming org must own.
  https://owasp.org/www-project-machine-learning-security-top-10/

### Case-specific primary sources (per worked archetype)

For HashiCorp BSL case:

- HashiCorp's public license-change announcement (company blog,
  August 2023).
- The Business Source License 1.1 text, maintained by MariaDB.
- OpenTofu project announcement and Linux Foundation project
  page.

For PyTorch Foundation case:

- Meta's research blog release of PyTorch (2016).
- The PyTorch Foundation announcement at the Linux Foundation
  (September 2022).
- The PyTorch Foundation member roster
  (<!-- verify-source: cite the current member page URL as of
  the submission date. -->).

### Practitioner implementation examples (not primary sources)

VeriSwarm's OSS-engagement playbook materials may be useful as
*practitioner examples* of the shapes described above. They are
not substitutes for primary sources on any specific case.
