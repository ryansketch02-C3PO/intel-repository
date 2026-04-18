# Stardust Chollima (BlueNoroff / DPRK) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Destructive** | 2 | -0 | **2** | 3 | -0 | **3** | **5** | 🟡 MEDIUM |
| **Disruptive** | 2 | -0 | **2** | 3 | -0 | **3** | **5** | 🟡 MEDIUM |
| **Cyber-Crime** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Supply Chain** | 4 | -0 | **4** | 4 | -0 | **4** | **8** | 🔴 HIGH |

**Overall A&D Priority Score: 7.8 / 10** 🔴

---

## Scoring Rationale

**Cyber-Crime — 5x5 (10/10)**
Stardust Chollima / BlueNoroff is DPRK's dedicated financial theft unit. They have stolen billions from banks, crypto exchanges, and financial institutions globally. For A&D, the relevant attack surface is: (a) any crypto/financial assets, (b) proprietary technology with black-market resale value, (c) financial data that enables social engineering for follow-on attacks. Defense contractors with significant financial operations, tech licensing revenue, or crypto treasury are prime targets. Target-specific financial motivation (5), maximum capability (5), custom tooling (-0).

**Espionage — MEDIUM**
Secondary mission — Stardust Chollima collects intelligence as a byproduct of financial intrusions, not as a primary mission. Sector association (3) for A&D.

**Supply Chain — HIGH**
Confirmed use of trojanized software packages (npm, PyPI, fake SDKs) distributed through developer supply chains. A&D software development teams using open-source packages are at risk from Stardust Chollima's poisoned package methodology. Fake IT workers (DPRK IT workers embedded in western companies) provide persistent supply chain access.

---

## A&D-Specific Risk Summary

Stardust Chollima's primary threat to A&D is financial: cryptocurrency theft, fraudulent wire transfers, and monetization of stolen financial data. Secondary threat: DPRK's "IT worker" program — North Korean nationals embedded as remote contractors at US tech firms, including some A&D-adjacent companies. If your company hired a remote software developer who turned out to be DPRK-affiliated, Stardust Chollima may have persistent access to your codebase and financial systems. Vet all remote contractors rigorously.
