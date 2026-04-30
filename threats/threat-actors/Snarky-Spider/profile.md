# Snarky Spider

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | Snarky Spider |
| **Other Names** | UNC6661, O-UNC-025 |
| **Type** | Financially-Motivated eCrime Group |
| **Origin** | United States / English-speaking (native) |
| **First Observed** | October 2025 |
| **Status** | Active |
| **Motivation** | Financial — data theft, extortion, cryptocurrency theft |
| **Threat Level** | 🔴 HIGH |
| **Com Affiliation** | ✅ Linked to The Com |

---

## Overview

Snarky Spider is a financially motivated extortion crew affiliated with **The Com**, tracked by CrowdStrike since at least October 2025. Like its close relative **Cordial Spider**, Snarky Spider uses voice phishing and fake SSO pages to breach identity platforms and tear through SaaS environments at speed — but it has a distinctly more aggressive operational personality.

Where Cordial Spider applies pressure through DDoS and extortion demands, Snarky Spider escalates to **direct physical harassment tactics including swatting** — placing false emergency calls targeting victim employees, including executives, to force payment.

CrowdStrike tracks the two groups separately due to distinct TTPs: different hours of operation, different phishing domain providers, different preferred operating systems, different data-leak sites, and different devices used for MFA registration. But the strategic goal is identical: fast identity compromise → SaaS traversal → data theft → extortion.

Per CrowdStrike SVP Adam Meyers: *"They've kind of taken [Scattered Spider's] playbook and they're using a lot of their techniques, but we haven't really seen the technical sophistication demonstrated by them that we saw from Scattered Spider. It's kind of the new generation of Scattered Spider."*

---

## Targets

| Dimension | Detail |
|---|---|
| **Primary Sectors** | Academic, Aviation, Retail, Hospitality, Automotive, Financial Services, Legal, Technology |
| **Geographic Focus** | United States (primary) |
| **Target Profile** | Organizations with IT helpdesks, SSO/identity platforms (Okta, Entra ID), SaaS-heavy environments |

---

## TTPs (MITRE ATT&CK)

### Resource Development
- **Phishing Infrastructure (T1583.001):** Registers domains mimicking corporate SSO and identity provider login portals — different domain providers and registrars compared to Cordial Spider
- **Residential Proxy Networks (T1090.002):** Uses Mullvad, Oxylabs, NetNut, 9Proxy, Infatica, NSOCKS to masquerade as legitimate users

### Initial Access
- **Vishing (T1566.004):** Calls target employees posing as IT support; directs to phishing pages capturing credentials, session tokens, or TOTP codes depending on workflow
- **Smishing / Phishing Email (T1566.001/T1566.002):** Used in combination with or as fallback to vishing lures

### Persistence
- **MFA Device Registration (T1098.005):** Registers attacker-controlled device under compromised account — differs from Cordial Spider in the type of device/token used
- **Alert and Notification Suppression:** Deletes internal alert emails to delay detection post-breach

### Defense Evasion
- **Legitimate Session Abuse:** Operates under valid SSO-authenticated sessions — avoids triggering user-agent or anomaly alerts
- **Living Off the Land:** No custom malware; abuses native SaaS APIs and legitimate admin tooling

### Collection
- **SaaS Repository Scraping (T1213):** Abuses Microsoft Graph API and SaaS search to harvest data from SharePoint, Salesforce, internal repositories
- Targets PII, confidential business documents, employee contact data

### Exfiltration
- **Browser/API Exfiltration (T1567.002):** Direct data download via browser or API under legitimate session
- **Cloud Staging:** Uses file-sharing services for data staging prior to exfil

### Impact
- **Financial Extortion (T1657):** Aggressive ransom demands; refuses negotiation in documented cases
- **DDoS:** Follow-on pressure against non-paying victims
- **Swatting (Physical Coercion):** Places false emergency calls to law enforcement against victim company personnel, including executives — the most aggressive escalation behavior observed from Com-affiliated extortion groups in 2026
- **Cryptocurrency Theft:** CrowdStrike adversary profile notes cryptocurrency theft activity in addition to data extortion

---

## Distinguishing Characteristics vs. Cordial Spider

| Dimension | Cordial Spider | Snarky Spider |
|---|---|---|
| **Also Known As** | BlackFile, CL-CRI-1116, UNC6671 | UNC6661, O-UNC-025 |
| **Data-Leak Site** | BlackFile (offline Apr 2026) | Separate DLS (details limited) |
| **Escalation Tactics** | DDoS | DDoS + **Swatting** |
| **Operating Hours** | Distinct pattern | Distinct pattern |
| **Domain Providers** | Specific set | Different set |
| **MFA Reg. Device** | Specific type | Different type |
| **Preferred OS** | Identified (limited public detail) | Different from Cordial Spider |
| **Aggression Level** | High | Very High |

---

## Infrastructure

| Indicator | Type | Note |
|---|---|---|
| Mullvad, Oxylabs, NetNut, 9Proxy, Infatica, NSOCKS | Proxy Networks | Shared with Cordial Spider |
| Fake SSO/IDP portals | Phishing Infrastructure | Registered via different providers than Cordial Spider |

---

## Relationships

| Actor | Relationship |
|---|---|
| **Cordial Spider** | Separate Com subgroup; overlapping TTPs, distinct operators |
| **Scattered Spider** | Playbook source; "new generation" |
| **SLSH** | Linked Com subset |
| **ShinyHunters** | Linked Com subset |
| **The Com** | Parent criminal ecosystem |

---

## Detection Opportunities

- **Unsolicited IT helpdesk calls** followed by unusual authentication events — same signature as Cordial Spider
- **New MFA device registration** from residential proxy / unusual ASN
- **SSO session from residential proxy range** with immediate bulk SaaS activity
- **Alert/inbox deletion** shortly after new login event
- **Law enforcement contact:** Swatting incidents directed at executives are a lagging indicator of active extortion; coordinate with facilities/security teams on unusual emergency responses

---

## Recommendations

1. **Treat swatting as a threat intel signal:** If executives report suspicious emergency service contact, escalate immediately to IR — this is a known Snarky Spider pressure tactic.
2. **Executive physical security:** Notify executives under active extortion situations about swatting risk; coordinate with local law enforcement proactively.
3. **Phishing-resistant MFA (FIDO2/passkeys):** TOTP is defeated in real-time by this group's AiTM phishing infrastructure.
4. **Out-of-band MFA device approval:** Any new device registration should require multi-step human verification outside the compromised channel.
5. **Identity platform hardening:** Audit Okta/Entra ID for unexpected new MFA devices, session tokens from proxy ASNs, and access from unusual geolocations.
6. **Helpdesk callback policy:** Mandatory verified callbacks for any helpdesk action — never trust the calling number.

---

## References

- [CrowdStrike — Snarky Spider Adversary Profile](https://www.crowdstrike.com/en-us/adversaries/snarky-spider/)
- [CrowdStrike — Defending Against Cordial Spider and Snarky Spider with Falcon Shield](https://www.crowdstrike.com/en-us/blog/defending-against-cordial-spider-and-snarky-spider-with-falcon-shield/)
- [CyberScoop — Two new extortion crews are speedrunning the Scattered Spider playbook](https://cyberscoop.com/crowdstrike-cordial-spider-snarky-spider-extortion-attacks/)

---

*Profile authored by C-3PO Intel | intel-repo threat tracking | Last updated: 2026-04-30*
