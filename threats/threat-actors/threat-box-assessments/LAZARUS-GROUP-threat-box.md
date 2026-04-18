# Lazarus Group (RGB / DPRK) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |
| **Destructive** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Disruptive** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Cyber-Crime** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |
| **Supply Chain** | 4 | -0 | **4** | 4 | -0 | **4** | **8** | 🔴 HIGH |

**Overall A&D Priority Score: 8.6 / 10** 🔴

---

## Scoring Rationale

**Espionage — HIGH (9/10)**
Lazarus Group's Operation Dream Job specifically targets aerospace and defense company employees with fake job offer lures to deliver malware and steal IP. Confirmed targeting of defense contractors in US, Europe, and South Korea for military technology. DPRK needs western defense technology for its weapons programs — ideology association (4) is conservative; could argue target-specific (5) given the explicit A&D targeting in Dream Job campaigns. Maximum capability (5), custom tooling (-0) with novel malware families per campaign.

**Destructive / Disruptive — MEDIUM**
Lazarus has demonstrated destructive capability (Sony Pictures, multiple wiper deployments). Against A&D, they are primarily espionage-focused. Destructive operations are possible but not the primary mission mode for this target type.

**Cyber-Crime — HIGH (9/10)**
Lazarus is the world's most prolific cryptocurrency thief — $3B+ stolen to fund DPRK's weapons program. They target financial systems, crypto exchanges, and DeFi protocols. Defense contractors with cryptocurrency treasury or technology assets (common in aerospace tech firms) are at risk. More broadly, any data valuable enough to sell to a third party triggers Lazarus's financial motivation — they will monetize A&D IP.

**Supply Chain — HIGH**
Dream Job campaigns target employees at A&D companies through LinkedIn and job boards — effectively a supply chain attack targeting the human element. Confirmed node.js and cross-platform backdoors delivered via fake technical interviews and coding tests.

---

## A&D-Specific Risk Summary

Lazarus Group is North Korea's Swiss Army knife — espionage, destruction, AND financial theft in one actor. For A&D, Operation Dream Job is the active, ongoing threat: your engineers are being targeted RIGHT NOW with fake LinkedIn job offers from "defense recruiters" that deliver malware during the interview process. Combined with their financial theft capability (any A&D firm with crypto treasury or valuable IP to sell is a target), Lazarus is a top-tier multi-vector threat across espionage, crime, and supply chain dimensions simultaneously.
