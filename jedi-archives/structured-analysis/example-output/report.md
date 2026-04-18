# Structured Analysis Report: Impact of Mobile Gaming on Gaming Marketplace Growth

> **Analysis ID**: 2026-03-26-mobile-gaming-marketplace-growth-impact
> **Date**: 2026-03-26
> **Techniques Applied**: Key Assumptions Check (v3), Analysis of Competing Hypotheses (v3), What-If Analysis (v3), Contrasting Narratives, Counterfactual Reasoning, Premortem (v3)
> **Mode**: Adaptive
> **Evidence Base**: 62 items
> **Iteration**: 3

---

## Executive Summary

Mobile gaming historically expanded the global gaming marketplace complementarily, growing the total addressable market to approximately $189 billion by attracting new demographics and geographies rather than cannibalizing existing console and PC segments [E02, E36, E37, E39]. This historical finding remains stable across all three iterations of this analysis and is the one conclusion that carries genuine confidence. However, the forward-looking assessment has undergone a fundamental revision: when engagement evidence is weighted equally with revenue evidence, the "complementary expansion with normal maturation" hypothesis (H1) drops from the most plausible explanation to the least plausible, accumulating 9 inconsistencies in the combined ACH assessment versus 3 for the leading hypothesis [ach-matrix.md]. No single hypothesis now captures the situation. The evidence requires a composite explanation.

Confidence in the overall assessment has been downgraded to **Low** -- reflecting not weak evidence but genuinely contradictory evidence. Revenue metrics show a stable $82B market [E16]; engagement metrics show a market in active contraction with retention declining year-over-year across all time horizons [E56], revenue concentrating in the top 50 games while the rest of the market declines [E57], and mobile gaming losing its position as the dominant mobile consumer spending category for the first time [E59]. These two evidence domains cannot be reconciled under a single narrative. The revenue picture is maintained by increasingly efficient extraction from a shrinking base -- "optimization and extraction, not growth" [E57].

The forward-looking trajectory bifurcates between two interpretations of structural fragility. The first is **structural collapse**: the compound system of ATT privacy disruption + DMA marketplace fragmentation + whale cohort erosion is already operating (three of four elements active [E58, E53, E60]) and could produce a 25-35% mobile revenue contraction by 2028. The second is **creative destruction**: the same structural forces eliminate the exploitative long tail, producing a smaller ($65-75B) but healthier mobile gaming market with better engagement, broader monetization models, and more sustainable developer economics. The evidence does not clearly favor one interpretation over the other, and this irreducible uncertainty is the central finding of the analysis.

---

## Key Judgments

| # | Judgment | Confidence | Supporting Techniques | Critical Assumptions | Indicators to Watch |
|---|---------|------------|----------------------|---------------------|-------------------|
| J1 | Mobile gaming expanded the total gaming market complementarily, adding approximately $80-100B in market size without causing proportional declines in console/PC revenue. **Stable across v1-v3** [E36, E37, E39]. | Moderate | ACH (H1 historical component), KAC (Linchpin 1: Supported), Counterfactual, Contrasting Narratives | Complementarity holds during contraction (untested) [PRIOR-v1: kac] | Console/PC revenue trend if mobile contracts |
| J2 | Mobile's current trajectory is structural decline, not normal maturation. Revenue stability masks engagement contraction: retention declining YoY [E56], market declining outside top 50 [E57], mobile overtaken by non-game apps in spending [E59]. **Upgraded from v2** [PRIOR-v1: kac] [PRIOR-v2: kac, what-if, premortem] | Moderate-Low | KAC v3 (Linchpin 3: upgraded to S), ACH v3 (H1 least plausible under equal weighting), What-If v3 (Medium-High plausibility), Premortem v3 | Revenue is a valid proxy (Unsupported); engagement metrics not compositionally biased | Revenue growth outside top 50; D1/D7 retention trends; non-game vs game spending gap |
| J3 | The free-to-play model was a genuine amplifier of mobile's market expansion but created structural trade-offs now materializing as the mechanism of market concentration. **Refined from v2** [PRIOR-v1: counterfactual] | High | Counterfactual ($82B actual vs $40-55B counterfactual), KAC (Linchpin 2), ACH v3 (premium outperforming F2P [E62]) | F2P effects disentanglable from hardware ubiquity | App store fee reductions; premium model viability; developer survival rates |
| J4 | Mobile gaming's geographic and demographic impact is real but overstated by headline figures. The global picture is bifurcated: mature markets in contraction, emerging markets still growing. **Upgraded from v2** -- bifurcation now most plausible single hypothesis (H4 ranked 1st in combined ACH). | Moderate | ACH v3 (H4 ranked 1st), Contrasting Narratives, KAC (emerging market evidence [E30, E32]) | Emerging markets follow APAC trajectory (Unsupported); engagement evidence has geographic bias | MENA/LATAM mobile revenue growth; emerging market ARPU trends |
| J5 | Platform convergence will eventually erode mobile's standalone market identity, but remains a 2028-2030+ phenomenon. **Unchanged across v1-v3.** | Low | ACH (H3), What-If, Contrasting Narratives (LOW resilience) | Cloud gaming achieves mainstream viability | Cloud gaming market share; cross-platform player percentage |
| J6 | The mobile gaming developer ecosystem is in structural contraction. 45K jobs lost, 26% of EU devs laid off, 30+ studios closed, salaries fell ~50% [E60]. **Strengthened from v2** [PRIOR-v2: kac, premortem] | Moderate | KAC v3 (Linchpin 6: Unsupported, strengthened), What-If v3 (content drought catalyst rated HIGH), Premortem v3 | Developer stress separable from cyclical correction; creative destruction alternative not fully tested | Studio closure rate; indie developer growth; content pipeline metrics |
| J7 | The compound system of ATT + DMA + whale erosion is already operating and self-reinforcing. Three of four elements are active. This is the current state, not a future risk. **New in v3.** | Moderate-Low | What-If v3, KAC v3 (Linchpin 7: Unsupported), Premortem v3 | ATT effects permanent [E58]; dampening forces insufficient | CPI trends; DMA marketplace adoption; whale spending trajectory |
| J8 | The forward trajectory bifurcates between structural collapse and creative destruction. The evidence does not clearly favor one over the other. **New in v3.** | Low | Premortem v3 (both interpretations viable), What-If v3 (collapse Medium-High), ACH v3 (composite required) | Creative destruction requires surviving firms to improve, not just extract | Top-50 turnover rate; new game breakout frequency; player satisfaction metrics |

### Confidence Level Definitions

| Level | Meaning | Criteria |
|-------|---------|----------|
| **High** | Well-corroborated by multiple independent sources; key assumptions well-supported | 3+ independent sources; linchpin assumptions rated Supported |
| **Moderate** | Plausibly supported but with notable gaps; some assumptions unverified | 1-2 sources with partial corroboration; some assumptions rated Correct-with-Caveats |
| **Moderate-Low** | Evidence supports but with significant qualifications; key assumptions under stress | Multiple sources but material caveats; at least one linchpin Unsupported |
| **Low** | Fragmented evidence; significant assumptions unsupported; alternative explanations viable | Single source or uncorroborated; linchpin assumptions rated Unsupported |

> Per ICD 203 Standard 2: Analysts must express and explain uncertainties associated with major analytic judgments.

---

## Analytical Framework

How each technique contributed to the assessment. Weights reflect relative influence on the key judgments -- not precision scores.

| Technique | Role | Weight | Direction | Key Contribution |
|-----------|------|--------|-----------|-----------------|
| Key Assumptions Check (v3) | Diagnostic | Primary | Challenging | Operationalized revenue-as-proxy diagnosis with parallel non-revenue assessment; upgraded stagnation from C to S based on five independent non-revenue indicators |
| Analysis of Competing Hypotheses (v3) | Diagnostic | Primary | Challenging | Demonstrated H1 drops from most plausible to least plausible when engagement evidence weighted equally -- the single most consequential finding of iteration 3 |
| What-If Analysis (v3) | Challenge | Primary | Challenging | Modeled ATT + DMA + whale erosion as a single reinforcing system; found three of four elements already in effect |
| Counterfactual Reasoning | Foresight | Supporting | Confirming | Confirmed F2P roughly doubled mobile's market contribution ($82B vs $40-55B counterfactual); established inherent scale-sustainability trade-off |
| Contrasting Narratives | Challenge | Supporting | Mixed | Validated primary expansion narrative historically (HIGH resilience) while exposing high-risk vulnerabilities in "3 billion gamers" inflation and whale exploitation extrapolation |
| Premortem (v3) | Challenge | Qualifying | Mixed | Upgraded structural fragility from counter-argument to viable alternative thesis with two branches (collapse vs creative destruction); identified confirmation bias in evidence collection |

**Note**: Three techniques are Challenging (KAC v3, ACH v3, What-If v3). The preponderance of Challenging findings reflects the iteration's targeted scope (structural fragility flags) and is consistent with the overall confidence downgrade to Low.

---

## Synthesis

### Cross-Technique Agreement

All six techniques converge on the core *historical* finding: mobile gaming expanded the total gaming market rather than cannibalizing existing segments. This has been stable across all three iterations. The KAC found complementarity "Supported" [E36, E37, E39]. The ACH ranked it first under revenue-only evidence. The Counterfactual confirmed additive market effects. This convergence provides the analysis's highest-confidence conclusion (J1: Moderate).

All v3 techniques also converge on the diagnosis that revenue-only assessment systematically overstates mobile gaming's current health. The KAC v3 operationalized this with a parallel non-revenue assessment. The ACH v3 demonstrated the diagnostic consequences (H1 inversion). The What-If v3 showed the compound system is already operating. The Premortem v3 validated the structural fragility thesis. This convergence supports the confidence downgrade to Low for forward-looking assessments.

### Cross-Technique Disagreement

1. **Collapse vs. creative destruction** [NEW in v3]: The What-If v3 models compound system leading to 25-35% revenue contraction (collapse, Medium-High plausibility). The Premortem v3 identifies an equally plausible alternative: the same forces produce a smaller but healthier market (creative destruction). The Counterfactual supports the creative destruction reading. Resolution: Both interpretations presented as the primary forward-looking uncertainty (J8). The evidence does not provide a basis for choosing between them.

2. **Geographic scope of engagement evidence** [NEW in v3]: The KAC v3 and ACH v3 relied on engagement metrics [E56, E57, E60] that are disproportionately Western/mature-market. The Premortem v3 flagged this: structural fragility may be geographically bounded. Regional data [E61] shows the Middle East leads in retention. Resolution: J4 now acknowledges bifurcation explicitly and flags geographic bias.

3. **Compositional effects in engagement metrics** [NEW in v3]: The Premortem v3 identified that declining aggregate retention [E56] may partly reflect market cleaning rather than genuine engagement erosion. The KAC v3 accepted decline at face value. Resolution: Flagged as interpretive uncertainty in J2; the scale (11,600 games, 1.48B MAU) limits the compositional explanation.

### Integrated Assessment

Mobile gaming's historical contribution to gaming marketplace growth is well-established and not in dispute. The critical question is what is happening now and what comes next.

The v3 analysis produced a fundamental revision: the "complementary expansion with normal maturation" thesis (H1) is now the least plausible explanation when engagement evidence is weighted equally with revenue evidence. This is not because H1 was wrong about the past but because it is wrong about the present. "Normal maturation" predicts revenue plateau with stable engagement; what the evidence shows is revenue plateau with declining engagement.

No single hypothesis replaces H1. The evidence points to a composite: mobile gaming historically expanded the market (H1 correct about past) through a structurally fragile model (H2 correct about current dynamics) that is now bifurcating by geography (H4) while the platform category begins to dissolve (H3).

The forward trajectory depends on whether the compound system (ATT + DMA + whale erosion, three of four elements active) produces collapse or creative destruction. Both are more plausible than the "normal maturation" framing.

**Overall confidence**: Low (downgraded from Moderate-Low in v2, Moderate in v1).

---

## Risks & Blind Spots

### Assumption Audit

1. **Engagement metrics accurately represent market health** (KAC v3, ACH v3) -- The analysis pivoted on non-revenue metrics in v3, but declining aggregate retention [E56] may partly reflect compositional effects. [Severity: HIGH -- affects J2, J7, J8]

2. **Revenue validly proxies market health** (all techniques) -- Formally Unsupported since v2. Now operationalized: every non-revenue metric contradicts revenue stability [E57]. [Severity: HIGH -- affects all judgments] [PRIOR-v1: kac] [PRIOR-v2: kac]

3. **The compound system is self-reinforcing without sufficient dampening** (What-If v3) -- Three dampening forces underweighted: AI-assisted UA, DMA fee reductions, web stores. [Severity: HIGH -- affects J7, J8]

4. **Complementarity is bidirectional under stress** (ACH, What-If) -- Academic evidence [E36, E37] demonstrated complementarity during growth. Untested under contraction. [Severity: MEDIUM -- affects J1] [PRIOR-v1: kac]

5. **Emerging market developer economics mirror mature market patterns** (KAC v3) -- No evidence on developer economics in growth regions. Ecosystem may be relocating, not contracting. [Severity: MEDIUM -- affects J4, J6]

### Evidence Balance

Evidence supporting expansion: ~15 items. Evidence supporting structural fragility: ~20 items after v3 additions. Ratio shifted from ~2:1 favoring expansion (v1) to ~1:1.3 favoring structural concerns. All 7 new v3 items confirmed fragility -- potential confirmation bias explicitly acknowledged [premortem.md]. The bias reflects the targeted search scope (three HIGH structural flags), not necessarily analytical error, but limits confidence in the v3 downgrade.

### Confidence Calibration

Confidence spans High (J3), Moderate (J1, J4), Moderate-Low (J2, J6, J7), and Low (J5, J8). Overall: Low. The v3 downgrade is driven by a structural finding (H1 inversion under equal weighting), not incremental evidence accumulation. Risk of overcorrection exists (creative destruction alternative, if correct, would warrant Moderate-Low). The inability to discriminate between collapse and creative destruction justifies Low.

### Strongest Counter-Argument

The mobile gaming market is undergoing creative destruction, not structural collapse, and the engagement metrics prove it. The top 50 games captured 80% of revenue growth [E57] because they are genuinely better products. The 24% turnover in the top 50 shows the market rewards innovation [E57]. The 45K jobs lost [E60] represent elimination of the exploitative long tail. Premium games outperforming F2P on acquisition [E62] shows evolution toward healthier models. DMA fee reductions will improve margins. The analysis confuses the death of the old model with the death of mobile gaming.

This counter-argument was upgraded from v2's "strongest counter-argument" to a "viable alternative thesis" in v3 [premortem.md].

### Missing Perspectives

1. **Emerging market developers**: No evidence on developer economics in India, MENA, LATAM, or Southeast Asia.
2. **Advertising ecosystem intermediaries**: No ad network operator perspectives on post-ATT adaptation.
3. **Regulatory enforcement**: No government regulator perspectives on loot box legislation timing.
4. **Platform holder financials**: No Apple or Google gaming-specific revenue disclosures.
5. **Adaptation and innovation evidence**: No systematic search for mobile gaming innovation or successful new launches.

### Internal Consistency

- ACH v3 rankings (H4 first, H1 last under combined weighting) consistently reflected across synthesis, executive summary, and judgments table.
- KAC v3 revenue-as-proxy diagnosis operationalized across techniques (resolved from v2).
- All referenced evidence IDs exist in the evidence registry.
- Confidence levels consistent across technique findings and integrated assessment.
- Remaining tension: Premortem v3's creative destruction alternative presented alongside What-If v3's collapse scenario without resolution. Characterized as irreducible uncertainty.

### Analytical Bias Check

1. **Confirmation bias in v3 evidence collection** [HIGH -- NEW]: All 7 new items confirmed structural fragility. Search queries targeted structural problems. The Premortem v3 flagged this explicitly.

2. **Anchoring** [MEDIUM -- evolved]: Monotonically decreasing confidence trajectory (Moderate to Moderate-Low to Low) warrants scrutiny as potential overcorrection. [PRIOR-v1: bias check] [PRIOR-v2: bias check]

3. **Authority bias on academic studies** [MEDIUM -- unchanged]: E36 and E37 retain significant weight but pre-date 2023-2025 stagnation. [PRIOR-v1: bias check] [PRIOR-v2: bias check]

4. **Remediation-direction bias** [MEDIUM -- carries from v2]: 18 of 18 new evidence items across v2 and v3 challenged the conventional line. [PRIOR-v2: bias check]

5. **Geographic bias** [MEDIUM -- NEW]: Engagement evidence disproportionately Western. Regional data [E61] shows different patterns.

---

## Monitoring Plan

Top indicators to track:

1. **Revenue growth outside top 50 mobile games**: Currently declining [E57]. Source: Sensor Tower / Deconstructor of Fun.
2. **D1/D7/D28 retention trends**: Currently declining [E56]. Source: GameAnalytics.
3. **Non-game vs game mobile spending gap**: Games overtaken ($81.8B vs $85B) [E59]. Source: Sensor Tower.
4. **Developer layoff/closure rate**: 45K lost through mid-2025 [E60]. Source: GamesIndustry.biz.
5. **iOS CPI trends by genre**: Strategy/RPG at $5.5-$6 [E52]. Source: AppsFlyer, Mapendo.

> Full monitoring plan: [Monitoring Plan](monitoring-plan.md)

---

## Problem Framing

**Original Statement**: Analyze the impact of mobile gaming on the growth of the gaming marketplace.

**Reframed Statement**: What role has mobile gaming played in shaping the size, composition, demographics, and competitive dynamics of the global gaming marketplace -- and what are the competing interpretations of that impact?

The reframing proved essential across three iterations: the original question's growth assumption was correct historically but increasingly misleading for the current trajectory. The v3 conclusion that a composite explanation is required depends on the reframing's broader scope.

---

## Evidence Base

The analysis drew on 62 evidence items across three iterations: 1 from conversation context, 43 from OSINT (iteration 1), 11 from OSINT (iteration 2, remediation), and 7 from OSINT (iteration 3, user-requested).

**Source breakdown:**
- **High reliability** (36 items): Newzoo, BCG, Sensor Tower, GamesIndustry.biz, academic journals, WEF, Apple, Business of Apps, GameAnalytics, Deconstructor of Fun, Mobile Dev Memo, TechCrunch, Wikipedia
- **Medium reliability** (17 items): MAF, Global Games Forum, SQ Magazine, TekRevol, Naavik, AInvest/Bain, Mapendo, Cometly, MDPI
- **Low reliability** (3 items): Wayline.io, Medium (Steve Owens), AppVertices
- **Analytical** (6 items): Cross-references to working artifacts

**Deception indicators**: Three sources flagged in v1 (E27, E11, E34) remain flagged. No new deception indicators.

**Key gaps**: Emerging market developer economics, advertising intermediary adaptation, regulatory enforcement intent, platform holder financials, adaptation/innovation evidence.

> Full evidence details: [Evidence Registry](evidence-registry.md)

---

## Revision History

### Judgment Revisions

| Judgment | v1 | v2 | v3 (Current) | Driver of Change |
|----------|----|----|--------------|------------------|
| J1 (Complementary expansion) | Moderate | Moderate | Moderate | Historical evidence unchallenged [E36, E37, E39] |
| J2 (Maturation vs structural) | Moderate; ambiguous | Moderate-Low; shifted structural | Moderate-Low; structural confirmed (5 non-revenue indicators) | v3: E56, E57, E59, E60, E58 |
| J3 (F2P as amplifier) | High; trade-offs noted | High; materializing | High; mechanism of concentration | v3: E57 (top-50 concentration), E62 (premium outperforming F2P) |
| J4 (Geographic/demographic) | Moderate | Moderate | Moderate; bifurcation most plausible single hypothesis | v3: ACH combined ranking; E61 regional divergence |
| J5 (Platform convergence) | Low | Low | Low | No new evidence across iterations |
| J6 (Developer ecosystem) | Not assessed | Moderate-Low (new) | Moderate (strengthened) | v3: E60 (45K jobs, 29% higher); studios closing pre-release |
| J7 (Compound system) | Not assessed | Not assessed | Moderate-Low (new) | v3: E58, E53, E60 -- three of four elements active |
| J8 (Collapse vs creative destruction) | Not assessed | Not assessed | Low (new) | v3: Premortem upgraded creative destruction to viable alternative |
| ACH H1 ranking | Rank 1 | Rank 1 (not re-run) | Rank 5 (least plausible under equal weighting) | v3: 8 engagement inconsistencies |
| Overall confidence | Moderate | Moderate-Low | Low | H1 inversion; composite required; collapse vs creative destruction undetermined |

### Evidence Delta

| Iteration | New Items | ID Range | Collection Focus |
|-----------|-----------|----------|-----------------|
| 1 | 43 | E02-E43 | Broad: market sizing, academic research, industry reporting, demographics, emerging markets |
| 2 | 11 | E45-E55 | Remediation: retention, player welfare, developer ecosystem, UA/advertising, regulatory |
| 3 | 7 | E56-E62 | User-requested: engagement benchmarks, market concentration, ATT compound effects, spending displacement, developer contraction, premium vs F2P |

### Findings Comparison

**What strengthened**: Structural fragility progressively validated (v1: possibility; v2: supporting evidence; v3: operationalized with H1 inversion). Compound system modeled as already operating.

**What weakened**: "Normal maturation" thesis (H1) progressively undermined -- least plausible under equal weighting in v3. BCG "recovery from slump" framing [E14] increasingly contradicted by non-revenue evidence.

**What is new in v3**: H1 inversion. Compound system model. Mobile gaming losing dominant mobile spending category [E59]. Creative destruction as viable alternative thesis. Composite explanation requirement. 45K jobs with comprehensive data [E60].

**What is unchanged**: Historical complementarity (J1). Platform convergence timeline (J5). F2P as genuine amplifier (J3). Academic evidence [E36, E37].

> Prior iteration reports: [report.v1.md](report.v1.md) | [report.v2.md](report.v2.md)

---

## Technique Detail

### Key Assumptions Check (v3)

**Purpose**: Operationalize revenue-as-proxy diagnosis, assess engagement evidence, evaluate ATT as compound system element.
**Process**: Re-evaluated 14 assumptions against 7 new evidence items. Constructed parallel non-revenue assessment. Added Linchpin 7. Upgraded Linchpin 3 from C to S.

#### Findings

Seven linchpin assumptions (up from six in v2):
1. **Complementarity** (Supported) -- unchanged [E36, E37, E39]
2. **F2P essential** (C) -- essential historically, now mechanism of concentration [E57, E62]
3. **Stagnation structural** (UPGRADED to S) -- five independent non-revenue confirmations
4. **Platform convergence** (Unsupported) -- unchanged
5. **Revenue as proxy** (Unsupported, operationalized) -- every non-revenue lens contradicts stability
6. **Developer ecosystem** (Unsupported, strengthened) -- 45K jobs, 26% EU devs [E60]
7. **UA infrastructure** (NEW -- Unsupported, linchpin) -- ATT broke feedback loop permanently [E58]

**Confidence**: Moderate-Low

> Full working artifact: [Key Assumptions Check v3](working/assumptions.md)

---

### Analysis of Competing Hypotheses (v3)

**Purpose**: Re-run ACH with engagement evidence to operationalize revenue-as-proxy diagnosis.
**Process**: Dual diagnosticity matrices (revenue-weighted, engagement-weighted) and combined equal-weight assessment.

#### Findings

Revenue-only: H1 ranked 1st (1 inconsistency). Engagement-only: H1 ranked last (8 inconsistencies). Combined: **H4 ranked 1st** (3 inconsistencies), H1 ranked last (9 inconsistencies). No single hypothesis suffices; composite explanation required.

**Confidence**: Low -- conclusions highly sensitive to metric choice.

> Full working artifact: [Analysis of Competing Hypotheses v3](working/ach-matrix.md)

---

### What-If Analysis (v3)

**Purpose**: Model ATT + DMA + whale erosion as a single reinforcing system.
**Process**: System architecture with causal links. Two new catalysts. Compound probability assessment.

#### Findings

Plausibility UPGRADED to Medium-High (from Medium in v2). Three of four elements already active [E58, E53, E60]. Scenario no longer requires future catalysts -- existing loop continues. Weakest link: mobile contraction propagating to console/PC.

**Confidence**: Medium-High plausibility for full scenario.

> Full working artifact: [What-If Analysis v3](working/what-if.md)

---

### Contrasting Narratives

**Purpose**: Test dominant expansion narrative against three competing frameworks.
**Process**: Four narrative frameworks stress-tested. Run in v1 only.

#### Findings

Primary narrative HIGH historical resilience. Extractive narrative MEDIUM (strengthened by v2/v3 evidence). Convergence LOW. Bifurcation MEDIUM (upgraded by ACH v3). Four convergence points identified.

**Confidence**: Moderate.

> Full working artifact: [Contrasting Narratives](working/narratives.md)

---

### Counterfactual Reasoning

**Purpose**: Test whether F2P was essential to mobile's market expansion.
**Process**: Minimal counterfactual (Apple maintains pre-2009 paid-app IAP policy). Run in v1 only.

#### Findings

Counterfactual mobile: ~$40-55B (vs $82B actual). Counterfactual total market: ~$140-160B (vs $189B). F2P roughly doubled contribution. Counterfactual market healthier but smaller. Console/PC causally independent. V3 evidence strengthens insight: trade-off now materializing [E57, E60].

**Confidence**: Moderate (quantitative); High (structural insight).

> Full working artifact: [Counterfactual Reasoning](working/counterfactual.md)

---

### Premortem (v3)

**Purpose**: Assume v3 assessment catastrophically wrong by September 2027.
**Process**: Failure narrative, five weaknesses, structured self-critique, structural fragility upgrade.

#### Findings

Five weaknesses (3 High, 2 Medium): compositional effects in engagement metrics, dampening forces underweighted, geographic bias, AI app spending cyclicality, creative destruction undertested. Structural fragility upgraded from counter-argument to viable alternative thesis with two branches.

Strongest self-critique: "You searched for problems. Not one of your seven new items supported the conventional line."

**Confidence**: High for weakness identification.

> Full working artifact: [Premortem v3](working/premortem.md)

---

## Sources

| Ref | Source | Retrieved | Reliability | Method |
|-----|--------|-----------|-------------|--------|
| [E02] | [Newzoo: Global games market $188.8B](https://newzoo.com/resources/blog/global-games-market-to-hit-189-billion-in-2025) | 2026-03-26 | High | OSINT |
| [E03] | [BCG: Video Gaming Report 2026](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E04] | [BCG: App store fee disruption](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E05] | [BCG: Cloud gaming projection](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E06] | [Newzoo: Console vs mobile CAGR](https://newzoo.com/resources/blog/global-games-market-to-hit-189-billion-in-2025) | 2026-03-26 | High | OSINT |
| [E11] | [Wayline.io: Predatory monetization](https://www.wayline.io/blog/predatory-monetization-mobile-gaming) | 2026-03-26 | Low | OSINT |
| [E12] | [Mother Jones: Whale economics](https://www.motherjones.com/media/2023/04/asphalt-video-games-microtransactions-loot-boxes-in-game-purchases-capitalism/) | 2026-03-26 | High | OSINT |
| [E13] | [Medium (Owens): Platform fee coercion](https://medium.com/@steveo98501/fighting-back-against-mobile-gamings-microtransaction-trap-37fafbfb7597) | 2026-03-26 | Low | OSINT |
| [E14] | [BCG: Industry recovery](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E15] | [Sensor Tower: Retention shift](https://sensortower.com/report/state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E16] | [GamesIndustry.biz: Mobile +1%](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E18] | [Sony shut Dark Outlaw Games](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E19] | [Epic 1,000+ layoffs](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E20] | [UK mobile employment -12.9%](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E25] | [TekRevol: Mobile revenue stats](https://www.tekrevol.com/blogs/mobile-game-revenue-statistics/) | 2026-03-26 | Medium | OSINT |
| [E26] | [Sensor Tower: Mobile $82B](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E28] | [MAF: Gender parity](https://maf.ad/en/blog/mobile-gamers-demographics/) | 2026-03-26 | Medium | OSINT |
| [E29] | [MAF: 50% spend nothing](https://maf.ad/en/blog/mobile-gamers-demographics/) | 2026-03-26 | Medium | OSINT |
| [E30] | [Global Games Forum: Emerging markets](https://www.globalgamesforum.com/features/by-the-numbers-the-markets-driving-mobile-gamings-next-boom-in-2025) | 2026-03-26 | Medium | OSINT |
| [E31] | [LATAM CPI](https://www.globalgamesforum.com/features/by-the-numbers-the-markets-driving-mobile-gamings-next-boom-in-2025) | 2026-03-26 | Medium | OSINT |
| [E32] | [MENA 18% YoY](https://www.globalgamesforum.com/features/by-the-numbers-the-markets-driving-mobile-gamings-next-boom-in-2025) | 2026-03-26 | Medium | OSINT |
| [E33] | [SQ Magazine: India +95M, 53% female](https://sqmagazine.co.uk/mobile-games-statistics/) | 2026-03-26 | Medium | OSINT |
| [E35] | [SQ Magazine: Console-to-mobile India](https://sqmagazine.co.uk/mobile-games-statistics/) | 2026-03-26 | Medium | OSINT |
| [E36] | [Entertainment Computing: Complementarity](https://www.sciencedirect.com/science/article/pii/S1875952121000422) | 2026-03-26 | High | OSINT |
| [E37] | [JAMS: Multihoming strengthens console](https://link.springer.com/article/10.1007/s11747-022-00893-4) | 2026-03-26 | High | OSINT |
| [E38] | [BCG: Platform convergence](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E39] | [WEF: 50-year platform history](https://www.weforum.org/stories/2020/11/gaming-games-consels-xbox-play-station-fun/) | 2026-03-26 | High | OSINT |
| [E40] | [BCG: Four converging trends](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E41] | [Newzoo: PC/console growth](https://newzoo.com/resources/blog/post-pandemic-growth-returns-for-pc-and-console-driven-by-premium-spending-and-changing-price-dynamics) | 2026-03-26 | High | OSINT |
| [E43] | [Naavik: 570% multi-platform playtime](https://naavik.co/digest/the-future-of-cross-platform-gaming/) | 2026-03-26 | Medium | OSINT |
| [E45] | [Business of Apps: D30 retention](https://www.businessofapps.com/data/mobile-game-retention-rates/) | 2026-03-26 | High | OSINT |
| [E46] | [Gibson et al.: Player guilt/regret](https://www.sciencedirect.com/science/article/pii/S0747563223001176) | 2026-03-26 | High | OSINT |
| [E47] | [JMIR: Loot box-gambling mediation](https://games.jmir.org/2024/1/e57304/) | 2026-03-26 | High | OSINT |
| [E48] | [MDPI: Gacha addiction](https://www.mdpi.com/2078-2489/14/7/399) | 2026-03-26 | High | OSINT |
| [E49] | [AInvest/Bain: 97% failure, 35K jobs](https://www.ainvest.com/news/gaming-industry-survival-crisis-studios-struggling-break-2509/) | 2026-03-26 | Medium | OSINT |
| [E50] | [Business of Apps: CPI, $50B ad spend](https://www.businessofapps.com/marketplace/mobile-game-marketing/research/mobile-game-marketing-costs/) | 2026-03-26 | High | OSINT |
| [E51] | [Business of Apps: ATT CPI spike](https://www.businessofapps.com/marketplace/mobile-game-marketing/research/mobile-game-marketing-costs/) | 2026-03-26 | High | OSINT |
| [E52] | [Mapendo: CPI by genre](https://mapendo.co/blog/mobile-games-cpi-2025) | 2026-03-26 | Medium | OSINT |
| [E53] | [Apple: DMA impacts](https://www.apple.com/newsroom/2025/09/the-digital-markets-acts-impacts-on-eu-users/) | 2026-03-26 | High | OSINT |
| [E54] | [ResearchGate: ATT impact](https://www.researchgate.net/publication/360275850) | 2026-03-26 | High | OSINT |
| [E55] | [Cometly: ATT 15-25% opt-in](https://www.cometly.com/post/ios-app-tracking-transparency-impact) | 2026-03-26 | Medium | OSINT |
| [E56] | [GameAnalytics: 2025 Benchmarks](https://investgame.net/wp-content/uploads/2025/02/2025-GameAnalytics-Mobile-Gaming-Benchmarks.pdf) | 2026-03-26 | High | OSINT |
| [E57] | [DoF/Sensor Tower: Seven Things](https://www.deconstructoroffun.com/blog/seven-things-the-2025-state-of-gaming-report-actually-tells-us) | 2026-03-26 | High | OSINT |
| [E58] | [Mobile Dev Memo: ATT Recession](https://mobiledevmemo.com/the-att-recession/) | 2026-03-26 | High | OSINT |
| [E59] | [TechCrunch: Apps overtook games](https://techcrunch.com/2026/01/21/consumers-spent-more-on-mobile-apps-than-games-in-2025-driven-by-ai-app-adoption/) | 2026-03-26 | High | OSINT |
| [E60] | [Wikipedia: 2022-2026 layoffs](https://en.wikipedia.org/wiki/2022%E2%80%932026_video_game_industry_layoffs) | 2026-03-26 | High | OSINT |
| [E61] | [GameAnalytics: Regional divergence](https://investgame.net/wp-content/uploads/2025/02/2025-GameAnalytics-Mobile-Gaming-Benchmarks.pdf) | 2026-03-26 | High | OSINT |
| [E62] | [DoF/Sensor Tower: Premium vs F2P](https://www.deconstructoroffun.com/blog/seven-things-the-2025-state-of-gaming-report-actually-tells-us) | 2026-03-26 | High | OSINT |

**Citation Methods:**
- **OSINT**: Retrieved via Firecrawl MCP or WebSearch/WebFetch fallback
- **ANALYSIS**: Analytical judgment produced via named technique protocol

---

*Generated by Structured Analysis Skill | 2026-03-26 | Analysis ID: 2026-03-26-mobile-gaming-marketplace-growth-impact | Iteration 3*
