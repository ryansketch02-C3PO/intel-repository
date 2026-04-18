# Volt Typhoon (PLA Cyberspace Force) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A1

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 3 | -0 | **3** | 4 | -0 | **4** | **7** | 🟡 MEDIUM |
| **Destructive** | 4 | -0 | **4** | 4 | -0 | **4** | **8** | 🔴 HIGH |
| **Disruptive** | 5 | -0 | **5** | 5 | -0 | **5** | **10** | 🔴 HIGH |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 4 | -0 | **4** | **8** | 🔴 HIGH |

**Overall A&D Priority Score: 8.1 / 10** 🔴

---

## Scoring Rationale

**Espionage — MEDIUM**
Volt Typhoon's primary mission is NOT intelligence collection — it is infrastructure pre-positioning for wartime disruption. Some data collection occurs as part of reconnaissance but it is a means to an end, not the primary objective. This distinguishes them from MSS actors (APT40, Salt Typhoon).

**Destructive — HIGH**
CISA confirmed Volt Typhoon has pre-positioned on US critical infrastructure — power grids, water systems, communications — with intent to "disrupt or destroy" in a Taiwan conflict scenario. Destruction is a confirmed mission objective; ideology association (4) for A&D as part of broader US military/defense ecosystem. Custom living-off-the-land TTPs (-0) make them extremely hard to detect and remove.

**Disruptive — 5x5 (10/10)**
This IS Volt Typhoon's mission. CISA's advisory explicitly states their goal: pre-positioning to execute disruptive attacks against US critical infrastructure during a military conflict with China (Taiwan). A&D manufacturing, defense contractor networks, and their power/communications dependencies are all in scope. Maximum intent and maximum capability. They may already be inside.

**Supply Chain — HIGH**
Volt Typhoon compromises SOHO routers and edge devices at small organizations to create living infrastructure for their C2 chain. Any small IT vendor or SOHO-networked supplier in a defense contractor's supply chain is a potential Volt Typhoon node.

---

## A&D-Specific Risk Summary

Volt Typhoon is a **dormant bomb** inside US critical infrastructure — and A&D is in the blast radius. They're not stealing your blueprints (that's APT40's job). They're pre-positioning to shut you down the moment China makes a move on Taiwan. The Five Eyes confirmed this is an active, ongoing campaign. The threat isn't that they'll attack tomorrow — the threat is that they may already be positioned and waiting for a geopolitical trigger. Every A&D contractor should be hunting for Volt Typhoon LOTL indicators NOW, not after the trigger is pulled.
