# REvil (Sodinokibi) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2
> **Note:** REvil is disrupted (key arrests 2021–2022). Scores reflect historical capability and alumni risk.

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 2 | -0 | **2** | 2 | -2 | **1** | **3** | 🟢 LOW |
| **Destructive** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Disruptive** | 3 | -0 | **3** | 4 | -2 | **3** | **6** | 🟡 MEDIUM |
| **Cyber-Crime** | 3 | -0 | **3** | 5 | -2 | **4** | **7** | 🟡 MEDIUM |
| **Supply Chain** | 4 | -0 | **4** | 5 | -1 | **5** | **9** | 🔴 HIGH |

**Overall A&D Priority Score: 5.6 / 10** 🟡
*(Downgraded from historical peak due to disruption. Alumni operating under LockBit/other RaaS elevate residual risk.)*

---

## Scoring Rationale

**Espionage / Destructive — LOW**
Pure financial motivation. No intelligence-gathering or destruction mandate.

**Disruptive / Cyber-Crime — MEDIUM**
At peak (2020–2021), REvil was responsible for 22% of all global ransomware. The Kaseya VSA attack — 1,500 organizations simultaneously — demonstrated maximum-scale disruptive capability. Disruption score downgraded from historical HIGH due to 2022 arrest/disruption. Alumni risk through LockBit and successor RaaS keeps this from dropping lower.

**Supply Chain — HIGH (9/10)**
The Kaseya VSA attack IS the textbook supply chain ransomware attack. One MSP platform compromised = 1,500 downstream victims. Many A&D companies use MSPs and IT management platforms. Historical capability is maximum (5); intent was demonstrably supply-chain-focused (4). Even disrupted, the methodology lives on in LockBit affiliates.

---

## A&D-Specific Risk Summary

REvil as an independent entity is disrupted and medium-priority. But their alumni, methodology, and tooling live on in LockBit and other successor RaaS platforms. The Kaseya playbook — compromise the IT management platform to hit every downstream client simultaneously — is directly applicable to A&D MSP and shared-services environments. Track REvil lineage actors within LockBit affiliate activity for residual risk.
