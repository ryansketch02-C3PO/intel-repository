# APT29 (Cozy Bear) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A1

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |
| **Destructive** | 1 | -0 | **1** | 2 | -0 | **2** | **3** | 🟢 LOW |
| **Disruptive** | 1 | -0 | **1** | 2 | -0 | **2** | **3** | 🟢 LOW |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |

**Overall A&D Priority Score: 7.4 / 10** 🔴

---

## Scoring Rationale

**Espionage — HIGH (9/10)**
APT29 / SVR is Russia's foreign intelligence service (SVR) cyber arm — the patient, sophisticated, long-dwell-time counterpart to APT28's aggressive GRU posture. SolarWinds demonstrated their ability to compromise thousands of organizations through a single supply chain vector. Confirmed targeting of defense contractors, government agencies, and technology companies. Ideology association (4) — they target organizations associated with US government and defense. Maximum capability (5), custom tooling (-0).

**Destructive / Disruptive — LOW**
APT29's mission is persistent intelligence collection. They are the most patient, quiet actors in the Russian cyber ecosystem — disruption or destruction defeats their purpose. Known for dwelling in networks for months/years without triggering alarms.

**Supply Chain — HIGH (9/10)**
SolarWinds IS the defining supply chain attack of the modern era. APT29 compromised SolarWinds' build pipeline to push malicious updates to 18,000+ customers including US government agencies and defense contractors. This defines their supply chain methodology — compromise a trusted upstream vendor, ride legitimate update mechanisms into thousands of downstream targets. Maximum capability and intent for A&D supply chain targeting.

---

## A&D-Specific Risk Summary

APT29 is the quietest and most dangerous long-term espionage threat to A&D. While APT28 makes noise, APT29 lives silently in your network for months. The SolarWinds playbook — compromise a software vendor's build system and let the update push do the work — applies directly to any A&D contractor using third-party software. Their presence may already be in your environment and you wouldn't know it. Prioritize threat hunting and supply chain software integrity auditing.
