# APT28 (Fancy Bear) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A1

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Destructive** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Disruptive** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |

**Overall A&D Priority Score: 8.6 / 10** 🔴

---

## Scoring Rationale

**Espionage — 5x5 (10/10)**
APT28 / GRU Unit 26165 is Russia's premier offensive cyber unit with a documented mandate targeting defense contractors, government networks, and military infrastructure. Multiple DOJ indictments confirm targeting of US defense industrial base. They have specifically targeted aerospace companies for technical intelligence supporting Russian military programs. Target-specific intent (5), zero diplomatic constraints (-0), maximum confirmed capability (5), custom toolset per campaign (-0).

**Destructive / Disruptive — MEDIUM**
APT28 has demonstrated destructive capability (NotPetya attribution disputed but GRU-linked operations include destructive components). Against US A&D they are primarily focused on espionage; however, in wartime/escalation scenarios their destructive and disruptive posture would likely elevate. Current scoring reflects peacetime operating pattern.

**Cyber-Crime — LOW**
State-sponsored espionage mission. No financial extortion activity.

**Supply Chain — HIGH**
APT28 extensively uses phishing of defense contractor personnel, targeting IT service providers, and compromising shared infrastructure. Their VPN exploitation campaigns and credential harvesting target the same supply chain ecosystem A&D companies operate within.

---

## A&D-Specific Risk Summary

APT28 is Russia's primary intelligence-collection threat against US A&D. With GRU backing, bespoke tooling, and a demonstrated record of targeting defense contractors, they represent a persistent 5x5 espionage threat. In an escalation scenario (Ukraine conflict spillover, direct Russia-US confrontation), their destructive and disruptive scores would jump significantly. Second-highest overall threat after APT40 for A&D espionage.
