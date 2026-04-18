# Scattered Spider

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | Scattered Spider |
| **Other Names** | UNC3944, 0ktapus, Muddled Libra, Starfraud, Scatter Swine, Octo Tempest, Storm-0875 |
| **Type** | Cybercriminal Group (loosely affiliated) |
| **Origin** | United States, United Kingdom, Western Europe |
| **First Observed** | 2022 |
| **Status** | Active (members arrested; operations ongoing) |
| **Motivation** | Financial — ransomware, extortion, data theft |
| **Threat Level** | 🔴 HIGH |

---

## Overview

Scattered Spider is a native English-speaking cybercriminal collective best known for devastating social engineering campaigns and high-profile ransomware attacks. Unlike most APT groups, they are **not nation-state actors** — they're Western, often very young (some members as young as 17), and are financially motivated.

They operate as a loosely affiliated network coordinating over Telegram and Discord. Their defining characteristic is **psychological manipulation** — they are arguably the world's best social engineers, capable of convincing IT helpdesk employees to hand over credentials, reset MFA, and bypass every technical control a company spent millions building.

They gained notoriety with the **MGM Resorts breach in September 2023**, which cost the company an estimated **$80 million** in damages, knocked casino systems offline for 10+ days, and locked guests out of their hotel rooms — all started with a single phone call.

---

## Targets

- **Primary Industries:** Telecommunications, Retail, Financial Services, Healthcare, Hospitality, Technology
- **Geographic Focus:** United States (primary), UK, Canada, Australia
- **Preferred Target Profile:** Large enterprises with IT helpdesks, Okta/SSO environments, cloud-heavy infrastructure

---

## Known Attacks

| Date | Target | Impact |
|---|---|---|
| 2022 | **Twilio & MailChimp** | Large-scale credential theft via phishing; downstream supply chain victims |
| Jun–Dec 2022 | **Telecom & BPO sector (C0027)** | SIM swap infrastructure built for future attacks |
| 2023 | **Riot Games** | Source code stolen; $10M ransom demand |
| Sep 2023 | **Caesars Entertainment** | ~65M loyalty accounts compromised; paid ransom |
| Sep 2023 | **MGM Resorts** | 10 days of outages; $80M+ damages; ALPHV/BlackCat ransomware on 100+ ESXi servers |
| 2024 | **Multiple retail/MSP targets** | Shifted to DragonForce, RansomHub, Qilin RaaS |
| 2025 | **MSPs via SimpleHelp RMM** | DragonForce ransomware; "one-to-many" supply chain attacks |

---

## TTPs (MITRE ATT&CK)

### Initial Access
- **Social Engineering (T1598, T1656):** Call IT helpdesk impersonating a legitimate employee; request password reset and MFA bypass
- **Phishing / Smishing (T1566):** Typosquatted domains mimicking Okta, SSO portals, VPNs, HR systems
- **SIM Swapping:** Intercept OTP codes sent via SMS to bypass MFA
- **MFA Fatigue / Push Bombing (T1621):** Flood targets with MFA approval requests until they accept

### Persistence
- **Remote Access Tools (T1219):** Deploy TeamViewer, AnyDesk, ScreenConnect
- **New Cloud VMs:** Spin up attacker-controlled VMs in victim cloud environments (unmonitored)
- **Fake Identity Providers:** Register malicious Okta IdPs via inbound federation feature (T1484.002)
- **Create Accounts (T1136):** New identities backed by fake social media profiles (T1585.001)

### Privilege Escalation
- **Credential Dumping (T1003):** Mimikatz, LaZagne, secretsdump — target AD NTDS.dit
- **IAM Abuse:** Create tokens, add roles, register MFA factors in AWS/Azure

### Discovery
- **AD Enumeration:** SharpHound, ADRecon, PingCastle; query Azure AD for users/groups/roles
- **Port Scanning:** RustScan to identify ESXi hosts, exposed services

### Lateral Movement
- **RDP / SSH:** Standard protocol abuse
- **SAMR Requests:** Modify Active Directory account details
- **VDI Pivoting:** Move into Virtual Desktop Infrastructure from SaaS footholds

### Collection & Exfiltration
- **SaaS Data Harvesting (T1530):** Target Salesforce, SharePoint for PII and network config
- **RClone / Dropbox (T1567.002):** Exfiltrate to cloud storage
- **MEGA.NZ:** Exfiltrate data to US-based data centers and MEGA
- **SSH transfers to Vultr/S3:** Blend with legitimate cloud traffic

### Impact
- **Ransomware Deployment:** ALPHV/BlackCat, DragonForce, RansomHub, Qilin
- **Delete Shadow Copies:** Stop VSS, corrupt backups before encryption
- **Monitor IR Calls:** Join incident response calls to track defender activity (T1078)

### Key Tools
| Tool | Purpose |
|---|---|
| Mimikatz | Credential dumping |
| LaZagne | Password extraction |
| Ngrok | C2 tunneling |
| Teleport | C2 (recent) |
| Evilginx | AiTM phishing / MFA bypass |
| SharpHound | AD enumeration |
| RClone | Data exfiltration |
| TruffleHog | Cloud secrets discovery |
| AnyDesk / TeamViewer / ScreenConnect | Remote persistence |
| Spectre RAT | Remote access trojan |
| Raccoon Stealer / Redline / Vidar | Credential/session theft |

---

## Infrastructure Patterns

Scattered Spider registers **short-lived domains** (typically active < 7 days) that impersonate victim organizations:

- **Pattern:** `victimname-okta[.]com`, `victimname-servicedesk[.]com`
- **Keywords used:** okta, sso, vpn, helpdesk, servicenow, mfa, corp, support, duo, rsa, internal, connect
- **Recent shift (2025):** Moving from hyphenated (`sso-company.com`) to subdomain-based (`sso.c0mpany.com`) to evade detection
- **~81% of domains impersonate technology vendors**

**Top Hosting ASNs:**
- AS39287 (ABSTRACT, FI)
- AS13335 (Cloudflare)
- AS399486 (VIRTUO, CA)
- AS14061 (DigitalOcean)
- AS20473 (Vultr/Choopa)

**Top Registrars:** NiceNIC, Hosting Concepts B.V., NameSilo, GoDaddy

---

## Ransomware Affiliations

| RaaS Platform | Period |
|---|---|
| ALPHV / BlackCat | 2023 (MGM, Caesars) |
| RansomHub | 2024 |
| Qilin | 2024 |
| DragonForce | 2024–2025 |

---

## Affiliations & Overlaps

- **LAPSUS$:** Significant TTP overlap; same social engineering style; Mandiant confirmed overlap at Sleuthcon 2023
- **0ktapus:** Early campaign name; same group
- **ALPHV/BlackCat:** RaaS partner (not same group)
- **DragonForce:** Current RaaS partner (2024–present)

---

## Arrests & Law Enforcement

- **2024:** Multiple members arrested in US, UK, and Europe
- **Tyler Buchanan (aka "TylerB"):** Key figure, indicted 2024
- **Noah Urban (aka "King Bob"):** Arrested 2024
- Members as young as **17** at time of arrest
- Despite arrests, decentralized structure allows operations to continue
- FBI + Europol cooperation ongoing

---

## CISA Advisory

- **Alert Code:** AA23-320A (Nov 16, 2023)
- **Joint Advisory:** FBI + CISA
- Reference: https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a

---

## Admiralty Scale Rating

| Field | Value |
|---|---|
| **Source Reliability** | A (multiple gov advisories, vendor reports, court records) |
| **Info Credibility** | 1 (confirmed, extensively documented) |
| **TLP** | TLP:CLEAR |
| **Overall Grade** | **A1** |

---

## References

- CISA Advisory AA23-320A (2023)
- Mandiant / Google — Sleuthcon 2023 presentation
- CrowdStrike — Scattered Spider profiling
- Check Point / Cyberint — IAB Report 2025
- ReliaQuest — Domain infrastructure analysis (2025)
- Darktrace — Evolving TTPs blog (2025)
- SEKOIA-IO — IOC dataset (GitHub)
- AHA / HHS HC3 — Threat Actor Profile (Oct 2024)
