# Charming Kitten (APT35 / IRGC-IO) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |
| **Destructive** | 2 | -0 | **2** | 2 | -1 | **2** | **4** | 🟢 LOW |
| **Disruptive** | 2 | -0 | **2** | 2 | -1 | **2** | **4** | 🟢 LOW |
| **Cyber-Crime** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Supply Chain** | 4 | -0 | **4** | 4 | -0 | **4** | **8** | 🔴 HIGH |

**Overall A&D Priority Score: 7.3 / 10** 🔴

---

## Scoring Rationale

**Espionage — HIGH (9/10)**
Charming Kitten is the IRGC-IO's strategic espionage unit — long-term credential harvesting, mailbox access, HUMINT collection. Confirmed targeting of aviation regulators, aerospace researchers, defense think tanks, and policy analysts. Their 2025 IRGC-IO Unit 50 leak revealed KPI-driven operations with quota targets for credential collection from defense-adjacent targets. BellaCiao and POWERSTAR implants for persistent access. Ideology association (4) — they target organizations associated with US defense and Israel. Maximum capability (5), custom tooling (-0).

**Destructive / Disruptive — LOW**
Charming Kitten's mission is patient intelligence collection. Disruption terminates their access. Not their role within the IRGC cyber apparatus (Handala handles destruction).

**Supply Chain — HIGH**
Charming Kitten harvests credentials from defense contractor employees and uses them to pivot into connected systems. Long-term mailbox access to a defense contractor executive can expose supply chain relationships, contract details, and subcontractor credentials — enabling further supply chain compromise.

---

## A&D-Specific Risk Summary

Charming Kitten is Iran's spy, not its hammer. For A&D, the threat is long-term, quiet credential harvesting and mailbox access — reading your emails, collecting your personnel's credentials, building a picture of your programs, contracts, and key personnel over months. Their fake job lure methodology directly overlaps with UNC1549's — if you're an aerospace engineer on LinkedIn, both groups are likely targeting you simultaneously. The 2025 IRGC leaks confirmed A&D-adjacent organizations are specifically in their quota targets.
