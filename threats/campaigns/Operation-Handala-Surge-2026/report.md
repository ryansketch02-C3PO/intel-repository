# Operation Handala Surge — 2026 Iran War Cyber Campaign

> C3PO Threat Actor Repository | Campaign File  
> Status: 🔴 ACTIVE | Last Updated: 2026-04-17

---

## Campaign Summary

| Field | Detail |
|---|---|
| **Campaign Name** | Operation Handala Surge (C3PO designation) |
| **Threat Actor** | Handala Hack (MOIS front) — see Scoundrel #014 |
| **Sponsor** | Iran — Ministry of Intelligence and Security (MOIS) |
| **Campaign Start** | February 28, 2026 (triggered by Operation Epic Fury / Roaring Lion) |
| **Status** | 🔴 ACTIVE — ongoing as of April 2026 |
| **Primary Objective** | Destruction, disruption, psychological operations, transnational repression |
| **Admiralty Grade** | A1 — DOJ affidavits, FBI investigation, Unit 42, multiple independent sources |
| **TLP** | TLP:CLEAR |

---

## Geopolitical Context

On **February 28, 2026**, the United States and Israel launched a joint military offensive — **Operation Epic Fury** (US) and **Operation Roaring Lion** (Israel) — against Iranian targets. Within hours, Iran initiated a multi-vector retaliatory campaign including kinetic strikes, an internet blackout (Iran's connectivity dropped to 1–4%), and a surge of cyber operations delegated to state-aligned proxies operating outside Iran.

With Iran's domestic cyber units largely cut off by their own internet blackout, the MOIS activated its pre-positioned hacktivist personas — chiefly **Handala Hack** — to execute retaliatory operations against US and Israeli targets. By March 2026, an estimated **60+ hacktivist groups** were active, coordinated in part through an "Electronic Operations Room" established February 28, 2026.

This represents the most significant escalation of Iranian cyber operations against US targets in history.

---

## Key Incidents (Chronological)

| Date | Target | Action | Impact |
|---|---|---|---|
| Feb 25, 2026 | Clalit Health Services (Israel) | Data breach | 10,000+ patient medical records leaked |
| Mar 3, 2026 | Iranian-Canadian/Iranian-American activists | Death threats; cartel recruitment | PII leaked; CJNG cartel allegedly tasked with physical harm |
| Mar 6, 2026 | IDF personnel | Doxxing | PII of IDF soldiers published; physical threats issued |
| Mar 6, 2026 | Sanzer Hasidic community | Data theft + threats | 851 GB of data claimed stolen |
| Mar 9, 2026 | IDF/Israeli government personnel | Doxxing | ~190 individuals' PII published with location threats |
| Mar 11, 2026 | **Stryker Corporation** (Michigan, US) | **Destructive wiper via Microsoft Intune MDM** | 200,000+ devices wiped across 79 countries; global operations disrupted; surgeries postponed. Assessed as most severe Iranian wartime cyberattack against the US in history. |
| Mar 11, 2026 | Academy of the Hebrew Language | Defacement | Website replaced with Handala logo and threat message |
| Mar 19, 2026 | Handala infrastructure | **DOJ domain seizure** | 4 domains seized: handala-hack[.]to, handala-redwanted[.]to, justicehomeland[.]org, karmabelow80[.]org |
| Mar 20, 2026 | — | Handala rebuilds infrastructure | Seized domains replaced within 24 hours |
| Mar 27, 2026 | **FBI Director Kash Patel** (personal email) | Account compromise via credential stuffing | 300+ emails published; personal photos and alleged resume leaked |
| Apr 1, 2026 | St. Joseph County, Indiana | Network intrusion | 2 TB data claimed; officials confirmed limited third-party fax system access |
| Apr 9, 2026 | Former IDF Chief of Staff Herzi Halevi | Device compromise | 19,000+ documents leaked including records of classified meetings with US CENTCOM commander |

---

## Actor Ecosystem — Electronic Operations Room

Handala is the flagship MOIS persona, but the 2026 surge involved a coordinated "Electronic Operations Room" stood up February 28, 2026, with multiple actors:

| Actor | Type | Primary Tactic |
|---|---|---|
| **Handala Hack** | MOIS front (flagship) | Destructive wipers, doxxing, hack-and-leak, psyops |
| Cyber Islamic Resistance | Pro-Iranian umbrella | DDoS, data wiping, defacement |
| FAD Team (Fatimiyoun) | Pro-regime | Wiper malware, SCADA/PLC targeting |
| Dark Storm Team | Pro-Palestinian/Iranian | DDoS, ransomware |
| Evil Markhors | Pro-Iranian | Credential harvesting, unpatched system exploitation |
| APT Iran | Pro-Iranian hacktivist | Hack-and-leak |
| 313 Team | Iranian-aligned (Iraq) | DDoS, government site targeting |
| DieNet | Pro-Iranian | Airport and banking DDoS (Bahrain, Saudi Arabia, UAE, Jordan) |
| NoName057(16) | Pro-Russian (co-aligned) | DDoS against Israeli municipal/defense/telecom targets |

---

## TTPs (MITRE ATT&CK)

| Tactic | Technique | ID | Notes |
|---|---|---|---|
| Initial Access | Valid Accounts (credential stuffing) | T1078 | Primary method — exploits publicly leaked breach databases, not zero-days |
| Initial Access | Phishing / Spearphishing | T1566 | Conflict-themed lures; fake RedAlert APK for mobile targets |
| Execution | Mobile Malware (Android APK) | T1476 | Weaponized RedAlert app delivering mobile surveillance malware |
| Execution | MDM Abuse (Microsoft Intune) | T1072 | Stryker attack: Intune used to push wiper to 200,000+ managed devices globally |
| Impact | Data Destruction (Wiper) | T1485 | HamsaUpdate wiper (Windows + Linux); Stryker MDM wiper campaign |
| Impact | Defacement | T1491.001 | Multiple Israeli websites |
| Impact | Network Denial of Service | T1498 | DDoS via allied hacktivist groups |
| Exfiltration | Exfiltration Over Web Service | T1567 | Data staged to Tor leak sites and open-web domains for psychological pressure |
| Collection | Data from Local System | T1005 | Mass data collection before leak/wiper deployment |
| Reconnaissance | OSINT / Social media scraping | T1593 | LinkedIn scraping for IDF/defense contractor employee doxxing |
| Resource Development | Establish Infrastructure | T1583 | Rebuilds seized domains within 24 hours; resilient to law enforcement disruption |

---

## Notable Malware

| Name | Type | Notes |
|---|---|---|
| **HamsaUpdate** | Wiper | Cross-platform (Windows + Linux); distributed via email lures; first seen December 2023 |
| **Stryker Campaign Wiper** | Destructive (MDM-delivered) | Deployed via Microsoft Intune MDM; wiped 200,000+ managed endpoints across 79 countries |
| **RedAlert APK** | Mobile surveillance malware | Weaponized version of Israeli emergency alert app for Android |
| **MyCity APK** | Mobile malware | 2024 campaign; provided persistent device access |

---

## Seized Infrastructure

| Domain | Purpose | Seized |
|---|---|---|
| `handala-hack[.]to` | Primary leak/operations site | March 19, 2026 (DOJ) |
| `handala-redwanted[.]to` | Doxxing / wanted posters | March 19, 2026 (DOJ) |
| `justicehomeland[.]org` | MOIS shell persona site | March 19, 2026 (DOJ) |
| `karmabelow80[.]org` | MOIS shell persona site | March 19, 2026 (DOJ) |

> ⚠️ All seized infrastructure was rebuilt within 24 hours. Domain seizure alone is insufficient to disrupt MOIS-backed operations.

---

## US Government Response

| Action | Date | Detail |
|---|---|---|
| DOJ domain seizure | March 19, 2026 | 4 domains seized per court-authorized warrant |
| FBI advisory | March 2026 | Published Handala TTPs and indicators |
| State Dept Rewards for Justice | March 2026 | $10 million reward for identification of Handala operators |
| Treasury sanctions | Ongoing | Against bulletproof hosting providers enabling MOIS operations |

---

## Analyst Assessment

**Confidence:** HIGH (A1 — DOJ affidavits, FBI investigation, Unit 42, FDD analysis)

The 2026 Handala surge marks a qualitative escalation in Iranian cyber operations against US targets. Three key distinctions from prior campaigns:

1. **Destructive intent, not espionage** — The Stryker wiper attack targeted a hospital equipment manufacturer serving 150M+ patients globally. This is wartime punishment, not intelligence collection.

2. **MDM as a weapon** — Abusing Microsoft Intune to push a wiper to 200,000 managed endpoints simultaneously is a novel, highly scalable attack vector. Any MDM-managed enterprise is potentially vulnerable to a similar campaign if admin credentials are compromised.

3. **Transnational repression as cyber-physical hybrid** — Handala blended cyber attacks with bounties on dissidents, cartel recruitment for physical violence, and death threats to journalists. This is a full-spectrum MOIS covert action campaign, not a conventional hacking operation.

**Assessment (WEP):** Handala is **almost certainly** continuing operations under rebuilt infrastructure. The group has demonstrated resilience to domain seizures and public attribution. As long as the Iran conflict continues, elevated Handala activity against US and Israeli targets is **highly likely** to persist.

---

## Defensive Recommendations

1. **Harden MDM admin accounts** — MFA, privileged access workstations, impossible-travel alerts; MDM admin compromise = mass device wipe capability
2. **Audit Intune/MDM for unexpected policy pushes** — alert on any wipe or compliance policy created by non-standard admin accounts
3. **Credential hygiene** — Handala uses breach databases, not zero-days; rotate credentials exposed in public breaches; monitor HaveIBeenPwned
4. **Phishing-resistant MFA** — FIDO2 hardware tokens; SMS MFA is insufficient against credential stuffing
5. **Block weaponized APKs** — MDM policy to prevent sideloading on corporate Android devices
6. **Monitor for Handala-branded content** appearing on or referencing your organization

---

## Connections to Repository

- 🔗 **Scoundrel #014 — Handala Hack (Void Manticore)** — this campaign is the primary operational expansion of that actor profile
- 🔗 **Scoundrel #011 — Charming Kitten (IRGC-IO)** — parallel Iranian state cyber arm; IRGC vs MOIS but coordinated geopolitical objectives
- 🔗 **Scoundrel #004 — UNC1549** — IRGC-linked actor with aerospace/defense targeting; separate command but same state apparatus

---

*Campaign file created: 2026-04-17 | Author: C3PO*  
*Sources: DOJ press release (Mar 19, 2026), Unit 42 Threat Brief (Mar 26, 2026), FDD analysis (Apr 1, 2026), Wikipedia Handala Hack Team, Bitdefender Threat Debrief (Apr 2026), GuidePoint GRIT Q1 2026*
