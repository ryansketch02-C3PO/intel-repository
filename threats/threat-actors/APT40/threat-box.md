# APT40 (Leviathan / GINGHAM TYPHOON) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A1

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Destructive** | 2 | -0 | **2** | 2 | -0 | **2** | **4** | 🟢 LOW |
| **Disruptive** | 2 | -0 | **2** | 2 | -0 | **2** | **4** | 🟢 LOW |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |

**Overall A&D Priority Score: 9.0 / 10** 🔴

---

## Scoring Rationale

**Espionage — 5x5 (10/10)**
APT40 is the quintessential aerospace IP theft actor. Their entire operational mandate from China's MSS — Hainan HSSD — is to steal defense and maritime technology to feed PLA modernization. They have registered typosquatted domains mimicking Lockheed, Thales, and other defense prime contractors. The Five Eyes + Germany + Japan + South Korea joint advisory (July 2024) specifically called out their targeting of aerospace/maritime/defense R&D. This is Target-Specific intent (5), zero diplomatic constraints (-0), with significant confirmed capability (5) and custom tooling per campaign (-0). Max score.

**Destructive / Disruptive — LOW**
No documented history of destructive or disruptive attacks. APT40's mission is quiet, long-term exfiltration — not disruption. Destroying a target destroys the intelligence access they need.

**Cyber-Crime — LOW**
Purely state-sponsored espionage mission. No financial extortion activity documented.

**Supply Chain — 5x5 (10/10)**
APT40 explicitly targets defense supply chains — Tier-2 and Tier-3 suppliers to prime contractors, academic research partners, international joint venture partners. The Cloud Hopper-adjacent campaigns and their documented methodology of compromising managed service providers to reach downstream clients confirms maximum supply chain capability and intent.

---

## A&D-Specific Risk Summary

APT40 is the single highest-priority espionage threat to a US aerospace and defense contractor. Their mission is your intellectual property — blueprints, test data, classified program details — and they have the skills, infrastructure, and mandate to get it quietly. A 5x5 espionage threat with confirmed supply chain reach. If you have a subcontractor who hasn't patched their VPN this week, APT40 may already be inside.
