# Scattered Spider — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 2 | -0 | **2** | 3 | -2 | **2** | **4** | 🟢 LOW |
| **Destructive** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Disruptive** | 4 | -0 | **4** | 5 | -1 | **5** | **9** | 🔴 HIGH |
| **Cyber-Crime** | 4 | -0 | **4** | 5 | -1 | **5** | **9** | 🔴 HIGH |
| **Supply Chain** | 3 | -0 | **3** | 4 | -1 | **4** | **7** | 🟡 MEDIUM |

**Overall A&D Priority Score: 6.4 / 10** 🟡

---

## Scoring Rationale

**Espionage — LOW**
Data exfiltration occurs but serves extortion leverage, not intelligence collection. No state-sponsored espionage mission.

**Destructive — LOW**
No documented intentional data destruction. Focus is on access and ransom/extortion.

**Disruptive — HIGH (9/10)**
The MGM incident ($80M damage, 10 days of casino chaos, all from one helpdesk phone call) defines Scattered Spider's impact ceiling. They are arguably the most capable social engineers in the non-state cybercriminal world. Large A&D companies with IT helpdesks, Okta SSO, and complex identity environments are their exact target profile. Maximum disruption capability (5); high intent against large enterprises (4).

**Cyber-Crime — HIGH (9/10)**
Pure financial motivation. Large organizations with the ability to pay multi-million dollar ransoms are their target. A&D prime contractors fit that profile exactly — sector association (4), max capability (5).

**Supply Chain — MEDIUM**
Scattered Spider's shift to targeting MSPs and IT contractors (2024–2025) creates supply chain risk. If an IT services provider supporting an A&D firm is compromised, Scattered Spider's "one to many" model means the prime is at risk. Less systematic than nation-state programs.

---

## A&D-Specific Risk Summary

Scattered Spider's threat to A&D is almost entirely dependent on one thing: whether your helpdesk can be socially engineered. They have bypassed technical controls at some of the most sophisticated organizations on earth using nothing but a phone call. For A&D, the specific risk is identity-layer compromise leading to ransomware deployment (currently DragonForce) and data extortion. If you have a Tier-1 helpdesk that resets MFA over the phone, Scattered Spider can be inside within hours of targeting you.
