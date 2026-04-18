# Cl0p — Oracle E-Business Suite Mass Exploitation Campaign (2025–2026)

> C3PO Threat Actor Repository | Campaign File  
> Status: 🟡 ONGOING (victim drip-posting into Q1 2026; exfiltration occurred late 2025) | Last Updated: 2026-04-17

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Cl0p Oracle EBS Mass Exploitation (C3PO designation) |
| **Threat Actor** | Cl0p (TA505 / FIN11) — see Scoundrel #018 |
| **Campaign Start** | Late 2025 (exploitation); victim disclosure ongoing through Q1 2026 |
| **Status** | 🟡 ONGOING — victim posting continues; exfiltration phase complete |
| **Primary Objective** | Mass data theft and extortion via zero-day exploitation of Oracle EBS |
| **Key CVE** | CVE-2025-61882 — Oracle E-Business Suite |
| **Extortion Evolution** | "Quadruple extortion" — encrypt + leak + C-suite direct contact + customer/partner notification threats |
| **Admiralty Grade** | B1 — GuidePoint GRIT, Netlas, multiple commercial threat intel sources |
| **TLP** | TLP:CLEAR |

---

## Overview

Cl0p's 2025–2026 Oracle EBS campaign follows the group's established mass-exploitation playbook — identify a critical zero-day in widely deployed enterprise software, exploit it at scale across hundreds of organizations simultaneously, steal data, then slowly drip-post victims over months to maximize extortion pressure.

This campaign mirrors the group's prior blockbuster campaigns:
- **2020–21:** Accellion FTA zero-day
- **2023:** MOVEit Transfer zero-day (CVE-2023-34362) — 2,500+ organizations affected

**CVE-2025-61882** affects Oracle E-Business Suite, a widely deployed ERP platform used across finance, manufacturing, government, and healthcare. Exploitation allowed mass data exfiltration without encryption — consistent with Cl0p's recent pivot away from file encryption toward pure data theft and extortion.

---

## Extortion Evolution: Quadruple Extortion

Cl0p escalated their extortion model in this campaign:

1. **Data encryption** (selective; some victims encryption-only)
2. **Public leak site posting** — victim names and data published on Tor site
3. **Direct C-suite contact** — compromised enterprise email accounts used to directly email executives with multi-million dollar demands
4. **Customer and partner notification threats** — threatening to contact victim's own customers and partners to amplify reputational damage

This shift to C-suite direct extortion via compromised email is significant: it bypasses the victim's security team entirely and applies personal pressure on decision-makers.

---

## Campaign Timeline

| Period | Activity |
|---|---|
| Late 2025 | Mass exploitation of CVE-2025-61882 across Oracle EBS installations |
| Late 2025 | Mass data exfiltration from hundreds of affected organizations |
| Q4 2025 | Cl0p begins drip-posting victims on Tor leak site |
| Q4 2025 | C-suite extortion emails sent via compromised enterprise email accounts |
| Q1 2026 | Victim posting continues (GuidePoint confirmed continued claims in Q1 2026) |
| Q1 2026 | Some victims still unaware of compromise |

---

## CVE Detail

| Field | Detail |
|---|---|
| **CVE** | CVE-2025-61882 |
| **Product** | Oracle E-Business Suite (Oracle EBS) |
| **Type** | Remote code execution / unauthorized data access |
| **Exploitation** | Mass exploitation; automated scanning and exfiltration |
| **Patch** | Oracle issued emergency patch; organizations with unpatched EBS during exploitation window should assume compromise |

---

## TTPs (MITRE ATT&CK)

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Exploit Public-Facing Application | T1190 | CVE-2025-61882 exploited at mass scale; automated tooling |
| Collection | Data from Information Repositories | T1213 | ERP data extraction: financials, HR, customer data, contracts |
| Exfiltration | Exfiltration Over Web Service | T1567 | Data staged and exfiltrated prior to any encryption |
| Impact | Financial Theft | T1657 | Quadruple extortion model |
| Impact | Data Encrypted for Impact | T1486 | Selective; some victims received encryption, others data-theft-only |
| Credential Access | Valid Accounts | T1078 | Compromised enterprise email for C-suite extortion delivery |
| Command & Control | Application Layer Protocol | T1071 | C2 via standard web protocols |

---

## Sector Impact

Organizations using Oracle EBS are concentrated in:
- **Finance and Banking** — payroll, accounts, financial reporting
- **Manufacturing** — supply chain, procurement, production data
- **Healthcare** — patient billing, HR, operations
- **Government** — procurement, HR, financial management
- **Technology** — ERP for large enterprise operations

**Aerospace relevance:** Many large aerospace and defense manufacturers and contractors use Oracle EBS for enterprise operations. Supply chain data, procurement records, and employee information may have been exfiltrated from aerospace-sector EBS installations during the exploitation window.

---

## Analyst Assessment

**Confidence:** MEDIUM-HIGH (B1 — commercial threat intel; no CISA advisory specific to CVE-2025-61882 found)

Cl0p's EBS campaign is consistent with the group's established pattern of identifying mass-exploitation opportunities in widely deployed enterprise platforms. The direct C-suite extortion evolution represents a meaningful escalation — it removes the security team as a buffer and applies personal financial and reputational pressure on executives who may not understand their obligations or options.

**Assessment (WEP):** Active victim posting is **likely** continuing into Q2 2026, consistent with Cl0p's historical habit of extending campaigns. Organizations that ran Oracle EBS in late 2025 without the CVE-2025-61882 patch should conduct compromise assessments.

---

## Connections to Repository

- 🔗 **Scoundrel #018 — Cl0p (TA505/FIN11)** — parent actor

---

## Defensive Recommendations

1. **Emergency patch Oracle EBS** for CVE-2025-61882 if not already done
2. **Assume compromise** if EBS was internet-facing and unpatched during the late 2025 exploitation window
3. **Conduct compromise assessment** — Cl0p often exfiltrates silently; organizations may not know they're compromised until they appear on the leak site
4. **Monitor for C-suite phishing** from compromised-looking corporate email addresses
5. **Check Cl0p's Tor leak site** for your organization (or use a threat intel service that monitors it)
6. **Segment ERP systems** from general network — EBS should never be directly internet-facing

---

*Campaign file created: 2026-04-17 | Author: C3PO*  
*Sources: GuidePoint GRIT Q1 2026 Ransomware Report (Apr 16, 2026), Netlas threat actor analysis (Jan 2026), Bitdefender Threat Debrief (Apr 2026)*
