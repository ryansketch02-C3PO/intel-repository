# Salt Typhoon (China MSS) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A1

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |
| **Destructive** | 1 | -0 | **1** | 2 | -0 | **2** | **3** | 🟢 LOW |
| **Disruptive** | 2 | -0 | **2** | 3 | -0 | **3** | **5** | 🟡 MEDIUM |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |

**Overall A&D Priority Score: 7.4 / 10** 🔴

---

## Scoring Rationale

**Espionage — HIGH (9/10)**
Salt Typhoon's confirmed mission: compromise global telecommunications infrastructure for SIGINT collection. They breached AT&T, Verizon, T-Mobile, and over 30 countries' telecom providers to access lawful intercept systems and collect call records. For A&D, the threat is indirect but severe: communications of defense contractor executives, engineers, and government liaison personnel flow through these compromised networks. Salt Typhoon can intercept those communications. Ideology association (4) — they target organizations with US government/defense nexus. Maximum capability (5), custom tooling (-0).

**Supply Chain — HIGH (9/10)**
Telecom infrastructure IS the supply chain for communications. By sitting inside AT&T, Verizon, and others, Salt Typhoon has persistent access to the communications backbone that connects A&D firms to their government customers, subcontractors, and each other. Any sensitive communication traversing a US major carrier is potentially accessible.

**Destructive / Disruptive — LOW-MEDIUM**
Salt Typhoon's mission is passive collection, not disruption. Disruption would reveal their access and terminate a valuable intelligence stream.

---

## A&D-Specific Risk Summary

Salt Typhoon is an indirect but uniquely dangerous threat to A&D. They don't hack your network — they hack your phone company. Communications between defense contractor executives and government customers, contract negotiation calls, personnel travel plans — all flowing through compromised telecom infrastructure that Salt Typhoon has been inside for years. Assume any communications traversing major US carriers may be intercepted. Encrypted communications (Signal, FIDO2-secured apps) are the mitigation; unencrypted calls and SMS are fully exposed.
