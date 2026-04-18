# Threat Box Methodology — C3PO Intel

> Based on Andy Piazza's Threat Box framework (SANS Whitepaper #39585)
> Adapted for Aerospace & Defense targeting by C3PO
> Reference: https://klrgrz.medium.com/quantifying-threat-actors-with-threat-box-e6b641109b11

---

## Purpose

Threat Box answers the question executives actually ask: **"Which threat actor should I care about, and why?"**

Standard risk models quantify organizational vulnerabilities. Threat Box quantifies the **human element** — specific actor intentions and capabilities against a defined target organization. The output is a scored, ranked, visual comparison of threat actors that can be delivered as a single-slide executive brief.

---

## Target Organization Profile (Our Calibration)

All scores in this repository are assessed against the following notional target:

> **A mid-to-large US aerospace and defense contractor** — ITAR-regulated, holding US government contracts, engaged in aircraft, spacecraft, missile, or defense system development, with a Tier-1/2 supplier network and classified/sensitive R&D programs.

This is our anchor. Every Intent score answers: *"Why would this actor target THIS organization?"*

---

## Attack Categories

Each actor is scored across five dimensions (four standard + one A&D-specific addition):

| Category | CIA Triad | Question |
|---|---|---|
| **Espionage** | Confidentiality | Can/will they steal our IP, data, or personnel information? |
| **Destructive** | Integrity | Can/will they corrupt, wipe, or damage our systems? |
| **Disruptive** | Availability | Can/will they take our systems offline? |
| **Cyber-Crime** | Financial | Can/will they extort us for near-term financial gain? |
| **Supply Chain** | All three | Can/will they compromise our suppliers, contractors, or shared software to reach us? |

> **Ransomware note:** If a ransomware campaign is a legitimate extortion attempt → supports Cyber-Crime score. If purely destructive in nature (wiper disguised as ransomware) → supports Destructive score.

> **IP theft note:** IP theft with no immediate monetization → Espionage. IP theft for immediate sale → Cyber-Crime.

---

## Scoring Dimensions

### Intent (1–5)
*Why would this actor target this organization with this attack type?*

| Score | Label | Definition |
|---|---|---|
| **5** | Target-Specific | Actor targets our org based on an objective achievable ONLY within our network (our specific blueprints, our classified contracts, our personnel) |
| **4** | Ideology Association | Actor targets our org based on association with a specific ideology (US government contractor, western defense, anti-Iran posture) |
| **3** | Sector Association | Actor targets our org based on sector (aerospace, defense, manufacturing, technology) |
| **2** | Regional Association | Actor targets our org based on geographic area of operations (North America, Western Europe) |
| **1** | Target of Opportunity | Actor targets our org simply because it is exposed and exploitable |

### Willingness Modifier (subtract from Intent)
*What constraints may limit the actor's intent?*

| Modifier | Label | Definition |
|---|---|---|
| **-0** | No Constraints | Strained diplomatic relations, previous hostilities, or significant economic disruption perceived by actor from US/western operations. **Applies to Russia, China, Iran, DPRK, and most criminal groups.** |
| **-1** | Moderate Constraints | Moderate diplomatic/economic ties with the US |
| **-2** | Strong Constraints | Strong diplomatic, economic, and security ties with the US |

### Capability (1–5)
*What evidence exists that this actor is capable of this attack type?*

| Score | Label | Definition |
|---|---|---|
| **5** | Significant Capability | Significant evidence of prior activity; confirmed by multiple trusted sources |
| **4** | Credible Capability | Credible evidence; moderately confirmed |
| **3** | Limited Capability | Some evidence; limited sources |
| **2** | Possible Capability | Very limited evidence; feasibility confirmed |
| **1** | Not Capable | No evidence of capability |

### Novelty Modifier (subtract from Capability)
*What indication of advanced, hard-to-defend-against skills are evident?*

| Modifier | Label | Definition |
|---|---|---|
| **-0** | Custom/Advanced | Custom toolset per campaign with demonstrated living-off-the-land capability. **Hardest to detect/defend.** |
| **-1** | Semi-Custom | Limited-availability or high-cost toolset used across multiple campaigns |
| **-2** | Commodity | Generally available toolset (Cobalt Strike, off-the-shelf malware, public exploits) |

> **Key insight:** Novelty measures *defensive difficulty*, not attacker sophistication. A nation-state using commodity tools (because the door is open) scores -2 even though they're technically elite. The point is: common tools = defenders have better detection signatures.

---

## Calculating Final Scores

```
Final Intent  = Intent Score - Willingness Modifier value
Final Capability = Capability Score - Novelty Modifier value

(Floor = 1. Scores cannot go below 1.)
```

**Composite Score** (per category) = Final Intent + Final Capability
- Minimum: 2 | Maximum: 10

**Overall A&D Priority Score** = Weighted average across categories:
- Espionage: 35% weight
- Supply Chain: 20% weight
- Destructive: 15% weight
- Disruptive: 15% weight
- Cyber-Crime: 15% weight

---

## Threat Level Thresholds (per category)

| Composite Score | Threat Level |
|---|---|
| 8–10 | 🔴 HIGH |
| 5–7 | 🟡 MEDIUM |
| 2–4 | 🟢 LOW |

---

## Rules of Engagement

1. **Evidence-based only.** Every score must be grounded in historical reporting. No "what-if monster" — don't score based on theoretical capability.
2. **Target-focused.** Scores are relative to our A&D target profile, not generic.
3. **Review cadence.** Scores should be re-evaluated quarterly or after major attribution events.
4. **Admiralty Grade applies.** If underlying intel is low-grade, flag the score confidence accordingly.
5. **Disrupted actors.** Scores reflect historical capability. Active threat level is noted separately.

---

## Output: The Threat Box Visual

Each actor's final Intent vs. Capability scores map to a 5x5 grid per attack category. The master threat matrix provides the ranked view across all actors and categories.

See: `threat-matrix.md` for the current scored output.
See: individual actor `threat-box.md` files for scoring rationale.

---

*Methodology by Andy Piazza (SANS) | A&D adaptation and implementation by C3PO*
*Tool reference: https://github.com/OllieJC/tbat (YAML export compatible)*
