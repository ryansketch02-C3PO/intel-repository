# Cl0p (TA505 / FIN11) — Threat Box Assessment

> **Target:** US Aerospace & Defense Contractor
> **Last Scored:** 2026-04-14 | **Analyst:** C3PO | **Admiralty Grade:** A2

---

## Scores

| Category | Intent | Will. Mod | Adj. Intent | Capability | Nov. Mod | Adj. Cap | Composite | Threat |
|---|---|---|---|---|---|---|---|---|
| **Espionage** | 2 | -0 | **2** | 3 | -1 | **3** | **5** | 🟡 MEDIUM |
| **Destructive** | 1 | -0 | **1** | 1 | -2 | **1** | **2** | 🟢 LOW |
| **Disruptive** | 3 | -0 | **3** | 4 | -1 | **4** | **7** | 🟡 MEDIUM |
| **Cyber-Crime** | 3 | -0 | **3** | 5 | -1 | **5** | **8** | 🔴 HIGH |
| **Supply Chain** | 4 | -0 | **4** | 5 | -0 | **5** | **9** | 🔴 HIGH |

**Overall A&D Priority Score: 6.6 / 10** 🟡

---

## Scoring Rationale

**Espionage — MEDIUM**
Cl0p exfiltrates data as leverage for extortion, not for long-term intelligence collection. Their MOVEit and Cleo campaigns stole data from defense-adjacent organizations (Ernst & Young, Shell, US federal agencies) but the motivation was financial. Some data exfiltrated from A&D companies could have intelligence value as a secondary effect, but that's not their primary mission. Regional association (2) is appropriate — they sweep broadly across North America.

**Destructive — LOW**
Cl0p's shift to encryption-less operations (pure data exfiltration) since 2023 demonstrates they want leverage, not destruction. No documented intentional destructive attacks. Ransomware encryption is disruptive, not destructive per the framework.

**Disruptive — MEDIUM**
Large-scale ransomware/extortion campaigns absolutely disrupt operations — the MOVEit campaign took hundreds of organizations offline temporarily. A&D companies running enterprise MFT software (likely) are directly in the blast radius of a Cl0p mass-exploitation event. Capability is high (4); intent is sector-level (3).

**Cyber-Crime — HIGH (8/10)**
This is Cl0p's primary mission. $500M+ in confirmed extortion proceeds. A&D companies are large enterprises with revenue and data worth paying for — sector association (3) to HIGH intent. Capability is maximum (5) with semi-custom tooling (-1, LEMURLOOT/DEWMODE are specialized but campaign-specific).

**Supply Chain — HIGH (9/10)**
This is Cl0p's signature vector. Their entire MFT exploitation model IS a supply chain attack — they find one vulnerable file transfer platform used by thousands of companies and breach them all simultaneously. A&D contractors heavily use enterprise MFT solutions for secure file transfer with government customers. Intent is ideology/sector (4), capability is maximum (5) with custom web shells (-0).

---

## A&D-Specific Risk Summary

Cl0p is the premier supply chain extortion threat to A&D. They don't hunt you individually — they find the software your entire industry uses (MOVEit, GoAnywhere, Cleo) and breach everyone at once. If your organization or any of your MFT vendors run unpatched file transfer software, you are statistically likely to be in Cl0p's next mass-exploitation batch. Zero-day development capability means the patch doesn't exist yet when they pull the trigger.
