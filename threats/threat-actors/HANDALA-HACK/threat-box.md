# Handala Hack (Void Manticore) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 4 | -0 | **4** | 4 | -1 | **4** | **8** | 🔴 HIGH |
| **Destructive** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Disruptive** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 3 | -1 | **3** | **7** | 🟡 MEDIUM |

**Overall A&D Priority Score: 8.1 / 10** 🔴

---

## Scoring Rationale

**Espionage — HIGH**
Handala steals data primarily as a psychological warfare tool — they publish it to embarrass and demoralise targets. Confirmed exfiltration of senior official emails, nuclear researcher names, intelligence officer records. Against a US A&D firm, they would steal and publish personnel data, project names, government contract details. Ideology association (4): they target any organization associated with US/Israeli defense posture.

**Destructive — 5x5 (10/10)**
Handala is Iran's MOIS destruction arm. ZeroShred, GoneXML, Hamsa, Hatef — simultaneous multi-wiper campaigns deployed via Group Policy across entire AD environments. The Stryker attack (March 2026) destroyed 200,000+ systems across 79 countries. A&D companies are explicitly targeted: Israeli Industrial Batteries (MoD supplier), weapons system designers. Custom wipers (-0) deployed with maximum intent (5). Max score.

**Disruptive — 5x5 (10/10)**
Mass wiper deployment and simultaneous operational disruption is their defining characteristic. They don't just destroy data — they paralyze organizations for weeks. Maximum intent, maximum capability. The Stryker incident (assessed as most severe Iranian cyber attack on US in history) is the benchmark.

**Cyber-Crime — LOW**
State-sponsored ideological mission. No ransomware or financial extortion activity.

**Supply Chain — MEDIUM**
Documented targeting of MoD suppliers and defense sub-contractors. Less systematic than Cl0p or APT40's supply chain programs, but demonstrated willingness to trace upstream from primary targets to their vendor ecosystems.

---

## A&D-Specific Risk Summary

Handala is the highest-priority **destructive** threat to a US A&D contractor. They don't want your money or your secrets — they want to burn your infrastructure to the ground and publish your employee names to Telegram. As the conflict between the US/Israel and Iran escalates, US defense contractors are explicit ideological targets. The Stryker incident — 200,000 systems destroyed, 150M patients affected — is a preview of what they're willing to do to a US defense firm.
