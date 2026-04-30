# Cordial Spider

## Identity

| Field | Details |
|---|---|
| **Primary Alias** | Cordial Spider |
| **Other Names** | BlackFile, CL-CRI-1116, UNC6671 |
| **Type** | Financially-Motivated eCrime Group |
| **Origin** | United States / English-speaking (native) |
| **First Observed** | October 2025 (active ops); February 2026 (retail/hospitality surge) |
| **Status** | Active |
| **Motivation** | Financial — data theft, extortion |
| **Threat Level** | 🟠 HIGH |
| **Com Affiliation** | ✅ Linked to The Com (moderate confidence — Unit 42 / Palo Alto Networks) |

---

## Overview

Cordial Spider is a financially motivated extortion crew affiliated with **The Com** — the same loose-knit English-speaking criminal collective that spawned Scattered Spider, SLSH, and ShinyHunters. Active since at least October 2025, the group runs a no-malware playbook: everything they do relies on social engineering, legitimate SaaS APIs, and living-off-the-land techniques.

Their defining signature is **voice phishing (vishing) from spoofed VoIP numbers**, impersonating IT helpdesk personnel to steal employee credentials, which they then use to traverse victims' entire SaaS ecosystems. Data is exfiltrated under the cover of legitimate SSO-authenticated sessions, making detection during the attack window extremely difficult.

Cordial Spider operated a victim-shaming / data-leak site called **BlackFile** — offline as of late April 2026. Extortion demands typically land in the **seven-figure range**. Non-paying victims have been subjected to DDoS attacks and, in some cases, **swatting of executives**.

CrowdStrike's SVP of Counter Adversary Ops described the group as "the new generation of Scattered Spider" — applying Scattered Spider's playbook without yet matching its technical sophistication.

---

## Targets

| Dimension | Detail |
|---|---|
| **Primary Sectors** | Retail, Hospitality, Healthcare, Technology, Transportation, Logistics, Wholesale, Financial Services |
| **Geographic Focus** | United States (primary) |
| **Target Profile** | Organizations with IT helpdesks, Okta/SSO environments, Salesforce, SharePoint |
| **Campaign Tempo** | Opportunistic; high volume since February 2026 |

---

## TTPs (MITRE ATT&CK)

### Resource Development
- **Phishing Infrastructure (T1583.001):** Registers phishing pages mimicking corporate SSO and identity provider portals
- **Antidetect Browsers:** Used to evade IP reputation filters
- **Residential Proxy Networks:** Mullvad, Oxylabs, NetNut, 9Proxy, Infatica, NSOCKS — blends into legitimate traffic

### Initial Access
- **Vishing (T1566.004):** Calls employees from spoofed VoIP numbers or fraudulent CNAM, impersonating IT support
- Directs targets to phishing pages capturing credentials and **TOTP codes** in real-time

### Persistence
- **MFA Device Registration (T1098.005):** Registers attacker-controlled device to bypass MFA in IAM platforms post-compromise
- **Executive Account Takeover:** Scrapes internal employee directories; pivots from standard accounts to high-privilege executive accounts via follow-on social engineering
- **Alert Suppression:** Deletes email alerts and notifications that would warn the organization of suspicious activity

### Collection
- **SaaS Data Discovery (T1213):** Abuses Microsoft Graph API (`Sites.Read.All`) to scrape SharePoint; uses Salesforce internal search
- Searches for files containing terms: `"confidential"`, `"SSN"`, PII
- Collects CSV datasets of employee phone numbers, internal business reports

### Exfiltration
- **Browser/API Exfiltration (T1567.002):** Data moved directly via browser downloads or API exports under legitimate SSO session cover
- **Salesforce API & SharePoint Download Functions:** Primary exfil methods
- **Staging Services:** LimeWire, MEGA

### Impact
- **Financial Extortion (T1657):** Demands sent via randomly generated Gmail accounts or compromised employee email addresses
- **DDoS:** Used as follow-on pressure against non-paying victims
- **Swatting:** Physical pressure tactic — false emergency calls made against company personnel including C-suite executives

---

## Infrastructure

| Indicator | Type | Note |
|---|---|---|
| `216.203.20[.]95` | IP | Attributed phishing traffic origin |
| Mullvad, Oxylabs, NetNut, 9Proxy, Infatica, NSOCKS | Proxy Networks | Used to evade geo/IP detection |
| BlackFile DLS | Data-Leak Site | Offline ~April 30, 2026 |
| Spoofed VoIP / fraudulent CNAM | Infrastructure | IT helpdesk impersonation calls |

---

## Relationships

| Actor | Relationship |
|---|---|
| **Scattered Spider** | Playbook closely mirrors; "new generation" per CrowdStrike |
| **Snarky Spider** | Separate Com subgroup with overlapping TTPs but distinct OpSec |
| **SLSH** | Linked Com subset |
| **ShinyHunters** | Linked Com subset |
| **The Com** | Parent criminal ecosystem |

---

## Known Incidents

| Date | Target | Impact |
|---|---|---|
| Oct 2025 – present | Multiple sectors | Data theft + 7-figure extortion; ongoing |
| Feb 2026 – present | Retail & Hospitality surge | Multiple RH-ISAC member orgs; Mandiant confirmed active IR |
| Apr 2026 | BlackFile DLS goes offline | Domain taken down; investigations ongoing |

---

## Detection Opportunities

- **Vishing pattern:** Employees report unsolicited IT helpdesk calls asking to visit a login link
- **New MFA device registration** from unusual ASN or residential proxy range
- **Microsoft Graph API abuse:** `Sites.Read.All` access from fresh or unexpected service principal
- **Salesforce bulk exports** during off-hours or from new device/location
- **Alert/email deletion** shortly after new login from unfamiliar location
- **Residential proxy IPs** in auth logs: Mullvad, Oxylabs, NSOCKS ASNs

---

## Recommendations

1. **Mandatory callback verification:** Any IT helpdesk call requesting credential action should require a verified callback through an official number — not the one provided by the caller.
2. **Phishing-resistant MFA:** Deploy FIDO2/passkeys; TOTP is actively harvested in real-time by this group.
3. **MFA device registration alerts:** Alert on any new MFA device registration and require human approval out-of-band.
4. **Graph API audit:** Monitor and restrict `Sites.Read.All` delegated permissions; alert on bulk SharePoint reads.
5. **Salesforce anomaly detection:** Alert on large exports or queries containing `"SSN"`, `"confidential"` from new sessions.
6. **Block residential proxies:** Where feasible, block known proxy ASNs at identity layer.

---

## References

- [CrowdStrike — Defending Against Cordial Spider and Snarky Spider with Falcon Shield](https://www.crowdstrike.com/en-us/blog/defending-against-cordial-spider-and-snarky-spider-with-falcon-shield/)
- [CyberScoop — Two new extortion crews are speedrunning the Scattered Spider playbook](https://cyberscoop.com/crowdstrike-cordial-spider-snarky-spider-extortion-attacks/)
- [RH-ISAC — Extortion in the Enterprise: Defending Against BlackFile Attacks](https://rhisac.org/threat-intelligence/extortion-in-the-enterprise-defending-against-blackfile-attacks/)
- [BleepingComputer — New BlackFile extortion gang targets retail and hospitality orgs](https://www.bleepingcomputer.com/news/security/new-blackfile-extortion-gang-targets-retail-and-hospitality-orgs/)
- [CyberScoop — BlackFile actively extorting data-theft victims in retail and hospitality](https://cyberscoop.com/blackfile-data-theft-extortion-retail-unit-42-rh-isac/)
- [Palo Alto Networks Unit 42 / RH-ISAC — CL-CRI-1116 reporting, April 23 2026](https://rhisac.org/threat-intelligence/extortion-in-the-enterprise-defending-against-blackfile-attacks/)

---

*Profile authored by C-3PO Intel | intel-repo threat tracking | Last updated: 2026-04-30*
