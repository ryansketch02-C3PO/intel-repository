# Scattered LAPSUS$ Hunters — Identity & SaaS Intrusion Campaign (2025–2026)

> C3PO Threat Actor Repository | Campaign File  
> Status: 🔴 ACTIVE | Last Updated: 2026-04-17

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Scattered LAPSUS$ Hunters — Identity/SaaS Campaign |
| **Threat Actors** | Scattered Spider (#013) + LAPSUS$ + ShinyHunters (merged Aug 2025) |
| **Type** | Cybercriminal — financially motivated; identity-centric intrusion |
| **Origin** | US/UK/Western (native English speakers; "The Com" criminal ecosystem) |
| **Merged As** | "Scattered LAPSUS$ Hunters" (announced August 2025) |
| **Campaign Start** | 2022 (individual groups); merged operations August 2025 |
| **Status** | 🔴 ACTIVE — sustained identity/SaaS intrusion operations into 2026 |
| **Primary Objective** | Financial extortion; data theft; identity compromise at scale |
| **Admiralty Grade** | B1 — GuidePoint GRIT, FBI, multiple commercial sources |
| **TLP** | TLP:CLEAR |

---

## Overview: The Merger

In **August 2025**, Scattered Spider, LAPSUS$, and ShinyHunters announced they would operate under a unified banner: **"Scattered LAPSUS$ Hunters."** GuidePoint's analysis confirmed this was less a formal merger and more a public acknowledgment of what had already been true — overlapping membership and sustained collaboration across all three groups, rooted in **"The Com"**, a loosely connected online criminal ecosystem where members share tools, infrastructure, and identities fluidly.

The three groups' individual histories:

| Group | Origins | Notoriety |
|---|---|---|
| **Scattered Spider** | May 2022 | 0ktapus campaign (9,931 accounts, 130+ orgs); MGM Resorts / Caesars attacks; $66M+ extortion |
| **LAPSUS$** | 2021 | Data extortion from Microsoft, NVIDIA, Samsung, Okta; teenage operators |
| **ShinyHunters** | 2020 | Mass cloud/SaaS database theft; AT&T (2024), Ticketmaster, hundreds of others |

Together they represent the most capable English-speaking criminal threat actor ecosystem currently active.

---

## Campaign Characteristics

### What Makes This Group Dangerous

1. **Native English fluency** — Unlike most ransomware groups, these operators speak English natively. Their social engineering is indistinguishable from legitimate IT staff, making vishing and impersonation attacks dramatically more effective.

2. **Identity-first, not malware-first** — Instead of deploying malware, they compromise identities: MFA fatigue attacks, SIM swapping, help desk impersonation, credential theft. Once they have a valid identity, they move like a legitimate user — almost impossible to detect with traditional tools.

3. **Speed** — Scattered Spider has compressed time-to-impact to **24–48 hours** from initial access. Traditional ransomware operators take days to weeks.

4. **SaaS/cloud focus** — They target cloud control planes and SaaS platforms (Okta, Salesforce, Snowflake, Mixpanel) rather than on-premise infrastructure — where most enterprise security monitoring is weaker.

5. **Scale** — Combined, the three groups have hit **100+ organizations** with $66M+ in documented extortion.

---

## Initial Access Methods

| Method | Description |
|---|---|
| **MFA Fatigue (Push Bombing)** | Flood target with MFA push requests until they approve out of frustration |
| **SIM Swapping** | Bribe telecom employees to transfer victim's phone number; intercept SMS MFA |
| **Help Desk Impersonation (Vishing)** | Call IT help desk posing as an employee; social engineer password reset / MFA bypass |
| **Credential Stuffing** | Use leaked credentials from breach databases |
| **Phishing / Smishing** | Native English lures; indistinguishable from legitimate IT communications |
| **Cloud API Abuse** | Access cloud platforms (Salesforce, Snowflake) via stolen session tokens |

---

## TTPs (MITRE ATT&CK)

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Phishing: Spearphishing via Service | T1566.003 | SMS, voice, app-based phishing |
| Initial Access | Valid Accounts: Cloud Accounts | T1078.004 | Primary post-compromise access method |
| Credential Access | MFA Fatigue | T1621 | Push notification bombing to exhaust victim |
| Credential Access | SIM Swapping | T1539 | Telecom employee bribery to hijack phone number |
| Credential Access | Adversary-in-the-Middle | T1557 | Session token interception |
| Defense Evasion | Valid Accounts | T1078 | Operate as legitimate users — evades most detection |
| Lateral Movement | Use Alternate Authentication Material | T1550 | Session tokens, OAuth abuse |
| Collection | Data from Cloud Storage | T1530 | Salesforce, Snowflake, Mixpanel, S3 buckets |
| Exfiltration | Exfiltration Over Web Service | T1567 | Direct exfil from cloud platforms |
| Impact | Financial Theft / Extortion | T1657 | Ransom demands; data leak threats |

---

## Notable Incidents

| Date | Target | Method | Impact |
|---|---|---|---|
| Mar–Jul 2022 | 130+ organizations (0ktapus) | SMS phishing → Okta compromise | 9,931 accounts compromised |
| Sep 2023 | MGM Resorts | Help desk vishing → Okta → ransomware | Multi-day operational outage; $100M+ losses |
| Sep 2023 | Caesars Entertainment | Social engineering | Paid ransom (reported ~$15M) |
| 2024 | AT&T | Cloud database (Snowflake) | 110M+ customer records stolen |
| 2024 | Ticketmaster | Snowflake | 560M+ records stolen |
| 2025 | ZenBusiness | Salesforce, Snowflake, Mixpanel | "Several terabytes" claimed |
| Feb 2026 | University of Pennsylvania (ShinyHunters) | — | 1M+ records; alumni system access |

---

## The Com Ecosystem

"The Com" is the underlying criminal network from which all three groups draw:

- Online forums and Discord servers where criminal operators collaborate
- Members move fluidly between group identities
- Share tools, initial access, infrastructure, and victim leads
- Primarily English-speaking; many members are teenagers or young adults in the US and UK
- Several members have been arrested (UK National Crime Agency arrests in 2022–2024); replacements emerge quickly

---

## Analyst Assessment

**Confidence:** MEDIUM-HIGH (B1 — GuidePoint, FBI, multiple commercial sources)

The August 2025 merger formalizes what was already operationally true. The combined Scattered LAPSUS$ Hunters entity represents the most capable English-speaking criminal threat actor currently tracked. Their identity-first approach is particularly dangerous for organizations that have invested heavily in perimeter and endpoint security but lag on identity hygiene and SaaS security.

GuidePoint's analysis confirmed: same TTPs, same infrastructure, same targeting patterns before and after the merger announcement — the brand changed, the operators didn't.

**Assessment (WEP):** Operations are **almost certainly** continuing at an elevated tempo. Organizations with exposed SaaS platforms, weak MFA implementations, or outsourced help desks are **highly likely** to be targeted. The addition of ShinyHunters' cloud database expertise and LAPSUS$'s insider access tradecraft makes the merged entity more capable than any individual predecessor.

---

## Connections to Repository

- 🔗 **Scoundrel #013 — Scattered Spider** — primary actor in this merged operation

---

## Defensive Recommendations

1. **Phishing-resistant MFA only** (FIDO2 hardware tokens) — eliminates MFA fatigue and SIM swapping attacks entirely
2. **Help desk identity verification protocols** — require in-person or video verification before MFA resets; never trust caller ID alone
3. **Okta / identity provider hardening** — monitor for impossible travel, new device enrollment, MFA bypass attempts
4. **SaaS security posture management (SSPM)** — audit Salesforce, Snowflake, Mixpanel, and other cloud data stores for excessive permissions and exposed APIs
5. **Session token binding** — prevent token reuse from unexpected IP addresses/devices
6. **Telecom carrier PIN** — add account PIN to prevent SIM swapping at the carrier level
7. **Monitor for rapid privilege escalation** in cloud environments — this group moves fast once inside

---

*Campaign file created: 2026-04-17 | Author: C3PO*  
*Sources: GuidePoint GRIT Q1 2026 Ransomware Report (Apr 16, 2026), FBI advisory on Scattered Spider, Netlas threat actor analysis*
