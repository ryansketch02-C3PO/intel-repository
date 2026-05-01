# Threat Actor Profile: Jasper Sleet (Storm-0287)

## Identity

| Field | Detail |
|---|---|
| **Scoundrel #** | 032 |
| **Primary Name** | Jasper Sleet |
| **Former Tracking Name** | Storm-0287 |
| **Aliases** | — |
| **Type** | State-Sponsored — **Insider Threat / Fraudulent Employment Scheme** |
| **Origin** | North Korea (DPRK) — workers physically located in North Korea, China, and Russia |
| **Active Since** | At least 2020 — expanding scope and sophistication through 2026 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **Motivation** | Revenue generation for the North Korean state; secondary: data theft, espionage, extortion |
| **Sectors Targeted** | IT, Critical Manufacturing, Transportation, Consumer Retail, Healthcare, Financial Services, Energy |
| **Geographic Focus** | United States (primary); expanding to global tech-adjacent roles |
| **Microsoft Consumer Accounts Suspended** | 3,000+ Outlook/Hotmail accounts confirmed and suspended |
| **DOJ Indictment** | January 3, 2025 — two DPRK nationals + three facilitators; $866,255 from 10 of 64 infiltrated US companies (2018–2024) |
| **Admiralty Grade** | A1 — confirmed by Microsoft Threat Intelligence, DOJ indictment, direct customer impact |
| **ATT&CK** | T1591, T1586, T1585.001, T1078, T1550, T1213, T1567, T1657 |

---

## Overview

Jasper Sleet is unlike any other threat actor in this repository. **They don't hack into your organization. They get hired by it.**

Operating on behalf of the North Korean state, Jasper Sleet deploys thousands of highly skilled IT workers who apply for legitimate remote jobs at Western companies using stolen identities, AI-fabricated personas, and a professional support infrastructure of facilitators. Once hired, they draw a salary — redirected to Pyongyang — while sitting inside your organization with authorized access to internal systems, intellectual property, and sensitive data.

The scheme exploits the structural shift to remote work and the increasing reliance on digital onboarding. The attacker never touches a firewall. They pass your background checks, join your Slack, access your SharePoint, and attend your all-hands. The breach is the employment contract itself.

**Scale:** Since 2020, US authorities have identified thousands of North Korean workers infiltrating companies across multiple industries — including over 300 companies, several Fortune 500s, and two government agencies. The DOJ indictment from January 2025 covered only a fraction of the activity. Revenue generated funds the DPRK's weapons programs, and the access creates a persistent intelligence collection capability.

**The AI escalation:** Since 2024, Jasper Sleet has integrated generative AI to dramatically improve operational scale:
- AI tools replace images in stolen identity documents
- AI-enhanced professional headshots from real worker photos
- Voice-changing software is being introduced for interview audio
- AI-generated tailored resumes match job postings with high fidelity
- Programmatic API scraping of HR platforms to identify and target open roles at scale

---

## The Three-Phase Attack

### Phase 1 — Pre-Recruitment (Discovery)

Jasper Sleet programmatically accesses HR platform APIs — particularly **Workday Recruiting Web Service** endpoints exposed through external career sites — to identify open roles, understand requirements, and submit applications at scale.

**Observed Workday API endpoints accessed:**
```
hrrecruiting/accounts/*
hrrecruiting/jobApplicationPackages/*
hrrecruiting/validateJobApplication/*
hrrecruiting/resumes/*
```

Behavior that distinguishes this from legitimate applicants: **multiple external accounts accessing the same API endpoints in a consistent, repeating pattern from the same malicious infrastructure** — not random job seekers browsing, but automated role discovery.

Applications are submitted using fake identities rented or stolen to match the geographic location of the target organization. AI generates tailored resume content that matches job description language with precision.

### Phase 2 — Recruiting

Once selected, the fake applicant communicates with hiring teams via **Microsoft Teams, Zoom, and Cisco Webex**. Key evasion techniques:
- **AI voice-changing software** during interviews — gradually reducing reliance on human facilitators
- **AI-enhanced professional photos** — real worker photos improved to appear polished and Western
- **Camera-off interviews** — citing bandwidth or equipment issues
- **US-based facilitators** intercept company hardware (laptops, MFA devices) sent to a domestic address
- **Laptop farms** — facilitators host multiple workers connecting remotely through a US-based physical machine
- **VPN/proxy services** (Astrill VPN observed) make traffic appear US-originated
- **DocuSign** used for offer letters and employment contracts — legitimizing the process

### Phase 3 — Post-Onboarding (Persistent Access)

Once hired, the actor has **legitimate, authorized access**. They:
- Sign into Workday from known Jasper Sleet infrastructure to update payroll details (redirected to DPRK)
- Access **Teams, SharePoint, OneDrive, Exchange Online** as a normal employee
- Install **RMM tools** (JumpConnect, TinyPilot, RustDesk, AnyDesk) to maintain remote access from their actual location
- Use **shared MFA devices** — one device completing MFA for multiple fake user accounts within the same tenant
- Maintain **long-lived session tokens** — persistence even through password resets
- Generate **impossible travel** — US login → immediate login from Russia, China, or Asia

**In escalation cases:** When discovered, some Jasper Sleet workers have threatened to leak proprietary data or internal communications unless paid — adding an extortion layer on top of the insider access.

---

## Tools & Infrastructure

| Tool / Method | Purpose |
|---|---|
| Astrill VPN | Mask true geographic location; appear US-based |
| Azure Virtual Desktop (AVD) | Remote into US-based facilitator machines |
| JumpConnect, TinyPilot | RMM — connect to company-issued hardware at facilitator's address |
| RustDesk, AnyDesk | RMM — remote access persistence |
| AI image tools | Enhance worker photos; modify identity documents |
| AI voice changers | Alter interview audio |
| Generative AI (LLMs) | Craft tailored resumes; respond to recruiter screening questions |
| DocuSign | Sign employment contracts, NDAs |
| Freelance platforms | Additional revenue channel; also used to source initial identity verifications |

---

## MITRE ATT&CK Mapping

| Phase | Technique | ID |
|---|---|---|
| Resource Development | Gather victim org info (job postings, HR platform recon) | T1591 |
| Resource Development | Compromise/establish fake accounts (social media, GitHub, LinkedIn) | T1586, T1585.001 |
| Resource Development | Identity procurement — stolen/rented real identities | T1586.003 |
| Initial Access | Valid accounts — legitimate employment credentials | T1078 |
| Persistence | Long-lived session tokens; RMM tools for remote access | T1550, T1219 |
| Collection | Data from information repositories (SharePoint, OneDrive, Exchange) | T1213 |
| Exfiltration | Data over web service channels | T1567 |
| Financial Theft | Payroll diversion to DPRK | T1657 |
| Impact | Extortion (escalation cases — threat to leak internal data) | T1657 |

---

## Detection Signals

Jasper Sleet generates no traditional malware indicators. Detection requires **behavioral and identity anomalies across HR, cloud, and endpoint telemetry.**

### Highest-Confidence Indicators

| Signal | System | Confidence |
|---|---|---|
| **Impossible travel alerts** for new hires — US login immediately followed by Russia/China/Asia login | Microsoft Entra ID / Azure AD | 🔴 HIGH |
| **Single MFA device** completing authentication for multiple user accounts within same tenant | MFA platform logs | 🔴 HIGH |
| **New hire payroll update** originating from known Jasper Sleet IPs / Astrill VPN egress | Workday telemetry | 🔴 HIGH |
| Repeated **Workday API calls** to `hrrecruiting/*` endpoints from single IP across multiple fake accounts | Defender for Cloud Apps — Workday connector | 🔴 HIGH |
| **RMM tools** (JumpConnect, TinyPilot, RustDesk, AnyDesk) installed within first weeks of employment | Endpoint telemetry | 🔴 HIGH |
| **Azure Virtual Desktop (AVD)** login patterns from Russian-based instances for new hire accounts | Sign-in logs | 🔴 HIGH |
| **Consistent ISP and geolocation** across multiple user account login events | AAD sign-in logs | 🟡 MEDIUM |
| Interview communications from **email addresses on suspicious infrastructure** | Exchange / email gateway | 🟡 MEDIUM |
| New hire accessing **large volumes of SharePoint/OneDrive data** early in tenure | Defender for Cloud Apps | 🟡 MEDIUM |
| **Camera-off video interviews** combined with audio quality inconsistencies | HR interview notes | 🟡 MEDIUM |

### Microsoft Defender XDR Hunting Queries

```kql
// Hunt: Impossible travel for recently hired accounts (within 90 days)
SigninLogs
| where TimeGenerated > ago(90d)
| where UserCreatedDateTime > ago(90d)  // New accounts only
| where RiskEventTypes contains "impossibleTravel"
| project TimeGenerated, UserPrincipalName, IPAddress, Location, ResultType

// Hunt: Single device completing MFA for multiple accounts
SigninLogs
| where AuthenticationDetails contains "MFA"
| summarize AccountCount = dcount(UserPrincipalName), Accounts = make_set(UserPrincipalName)
  by DeviceDetail, IPAddress, bin(TimeGenerated, 1d)
| where AccountCount > 2  // Single device, multiple accounts

// Hunt: New hire Workday payroll/bank changes from suspicious IPs
CloudAppEvents
| where Application == "Workday"
| where ActionType has_any ("Add", "Change", "Assign", "Create", "Modify")
  and ActionType has_any ("Account", "Bank", "Payment", "Tax")
| where AccountObjectId in (new_hire_account_ids)  // Parameterize with HR list
| project Timestamp, AccountId, ActionType, IPAddress, CountryCode

// Hunt: External Teams communications from suspicious IPs during hiring
CloudAppEvents
| where Application == "Microsoft Teams"
| where IsExternalUser == true
| where IPAddress in (known_jasper_sleet_ips)  // Cross-reference threat intel
| summarize make_set(ActionType) by IPAddress, AccountId, bin(Timestamp, 1d)

// Hunt: Suspicious email from hiring-related subject lines
EmailEvents
| where Subject has_any ("Interview", "Application", "Offer", "Onboarding")
| where SenderIPv4 in (known_jasper_sleet_ips)
| project Timestamp, SenderMailFromAddress, SenderDisplayName, RecipientEmailAddress, Subject
```

---

## A&D / ITAR Relevance

**CRITICAL for aerospace and defense.** This threat profile is particularly severe for A&D organizations because:

1. **ITAR violation exposure** — A foreign national (North Korean) with authorized insider access to controlled technical data, export-controlled designs, or classified systems constitutes a potential ITAR violation regardless of whether data was exfiltrated. The fact of insider access to ITAR-controlled materials requires legal review and possible government reporting.

2. **IT contractor roles are the target** — Jasper Sleet specifically targets software development, DevOps, cloud, and IT support roles — exactly the roles that get provisioned with elevated access to source code repositories, engineering environments, CAD systems, and internal tooling.

3. **Defense clearance adjacency** — While cleared positions are not directly targeted (cleared roles require in-person vetting), IT contractors often work alongside cleared personnel and may have access to systems, networks, or facilities that are clearance-adjacent.

4. **Long-duration access = deep collection** — Unlike a malware breach which can be contained, an employed insider with authorized access over 6–18 months can systematically collect program data, personnel information, organizational charts, and technical documentation through entirely normal work activity.

5. **Extortion escalation risk** — If discovered and confronted, Jasper Sleet workers have threatened to leak internal communications or proprietary data. In an ITAR-regulated environment, any such threat has regulatory and legal dimensions beyond typical incident response.

---

## Defensive Recommendations

| Priority | Action |
|---|---|
| 🔴 IMMEDIATE | **Train HR and recruiting teams** — They are the first line of defense. Teach recognition of: camera-off interview requests, inconsistent background details, unusual urgency around payroll/hardware, inconsistent audio quality |
| 🔴 IMMEDIATE | **Mandate live video for all IT contractor interviews** — No exceptions. Camera must be on throughout technical and HR interviews |
| 🔴 HIGH | **Verify identity documents independently** — Do not rely on digital-only submission; cross-reference with third-party identity verification services; require in-person identity verification for IT roles with elevated access |
| 🔴 HIGH | **Monitor Workday API access** — Enable Defender for Cloud Apps Workday connector; alert on repeating API call patterns from external accounts |
| 🔴 HIGH | **Alert on new hire impossible travel** — Immediately investigate any impossible travel alert for accounts < 90 days old |
| 🔴 HIGH | **Audit MFA device sharing** — Alert on single device completing MFA for 2+ accounts within same tenant |
| 🔴 HIGH | **Restrict RMM tools** — Allowlist RMM tools; flag JumpConnect, TinyPilot, RustDesk installation by new employees |
| 🟡 HIGH | **Hardware delivery verification** — Do not ship company hardware to residential addresses without verification; consider requiring in-office hardware pickup for elevated-access roles |
| 🟡 HIGH | **Alert on VPN/proxy usage by new hires** — Particularly Astrill VPN; flag sessions originating from known VPN egress IPs |
| 🟡 MEDIUM | **Restrict SharePoint/OneDrive access scope** for new hires during probationary period — Apply least privilege provisioning |
| 🟡 MEDIUM | **Cross-reference job applicant infrastructure** — Enable Defender for Cloud Apps connectors for DocuSign, Zoom, and Webex; monitor external accounts against threat intel |

---

## Related North Korean Actors

| Actor | Type | Relationship |
|---|---|---|
| **Storm-1877** | DPRK IT worker scheme | Similar fraudulent employment operation; Microsoft tracks separately |
| **Moonstone Sleet** | State-sponsored / employment fraud | DPRK actor with overlapping employment fraud TTPs; also deploys malware |
| **Sapphire Sleet** | State-sponsored / social engineering | Separate DPRK actor; targets crypto professionals via fake job offers; deploys macOS credential stealers |
| **Stardust Chollima (#002)** | State-sponsored / financial crime | DPRK financial theft; sometimes overlapping target profile |
| **Lazarus Group (#003)** | State-sponsored / espionage + cybercrime | Top-tier DPRK APT; broader mandate; Jasper Sleet focused on employment infiltration specifically |

---

## References

- [Microsoft Security Blog — Jasper Sleet: North Korean remote IT workers' evolving tactics (June 30, 2025)](https://www.microsoft.com/en-us/security/blog/2025/06/30/jasper-sleet-north-korean-remote-it-workers-evolving-tactics-to-infiltrate-organizations/)
- [Microsoft Tech Community — Microsoft Defender Experts Disrupt Jasper Sleet's Insider Access Campaign (December 18, 2025)](https://techcommunity.microsoft.com/blog/microsoftsecurityexperts/microsoft-defender-experts-disrupt-jasper-sleets-insider-access-campaign/4478112)
- [Microsoft Security Blog — Detection strategies across cloud and identities against infiltrating IT workers (April 21, 2026)](https://www.microsoft.com/en-us/security/blog/2026/04/21/detection-strategies-cloud-identities-against-infiltrating-it-workers/)
- [DOJ — Indictment of two DPRK nationals and three facilitators (January 3, 2025)](https://www.justice.gov/opa/press-release/file/2025/01/jasper-sleet-indictment)
- [CISA — North Korean IT Workers Advisory](https://www.cisa.gov/resources-tools/resources/north-korean-remote-it-worker-advisory)

---

*Profile created: 2026-05-01 | Author: C3PO | Admiralty Grade: A1 | TLP: WHITE*
