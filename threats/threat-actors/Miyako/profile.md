# 🐱 Miyako — Threat Actor Profile

## Identity

| Field | Details |
|---|---|
| **Primary Name** | Miyako |
| **Aliases** | mommy, Miya, nastya_Miyako, Minari, miyak0, miyak000 |
| **Type** | Initial Access Broker (IAB) / Financially Motivated Cybercriminal |
| **Attributed To** | Unknown individual / Solo operator (self-described) |
| **Active Since** | August 2024 |
| **Status** | 🔴 Active |
| **Motivation** | Financial — Access-as-a-Service; sale of stolen credentials and network access |
| **Scoundrel #** | #012 |

---

## Operator Profile

Miyako is a **prolific solo Initial Access Broker** operating primarily on **BreachForums**, where they have attained elevated "GOD" status with a reputation score of **1,529**, **230+ posts**, and **102 threads** since joining in August 2024. This level of forum standing requires consistent, verified follow-through on sales — Miyako is not a low-credibility actor making empty claims.

Self-described lone operator. Claims to **never attack CIS countries** — a standard operational constraint signaling likely Eastern European/Russian-sphere affiliation, though origin is unconfirmed.

In **January 2025**, Miyako published a paid **intrusion manual** on BreachForums — a step-by-step guide for replicating their access broker operation, including walkthroughs, tools, commands, and monetization strategies. A copy was leaked to VirusTotal and analyzed by Intel 471. The guide demonstrates a level of operational sophistication rare among solo actors.

Intel 471 formally designated Miyako as an Emerging Threat in **June 2025**, citing advanced TTPs and high-profile targeting.

---

## Target Profile

| Field | Details |
|---|---|
| **Primary Sectors** | Government, Defense Industrial Base, Telecommunications, Critical Infrastructure, Financial Services |
| **Secondary Sectors** | Healthcare, Logistics, Travel/Hospitality, Energy |
| **Geographies** | United States (primary), Europe, Middle East (Saudi Arabia), Asia (China, Iran) |
| **Pricing Model** | Low-cost access listings: $200–$800 per entry, non-negotiable |
| **Buyer Profile** | Nation-state actors, ransomware affiliates, espionage groups — Miyako sells the door, others walk through it |
| **Aerospace/Defense Relevance** | **HIGH** — Confirmed sale of access to a US DoD EMP defense contractor; 16 US companies/institutions compromised in first 2 months of 2025 |

---

## Confirmed Victim Access (Publicly Documented)

| Date | Target | Access Type | Price | Source |
|---|---|---|---|---|
| Jan 2025 | China Telecom data center | Root access to firewall server | Undisclosed (serious buyers only) | DoingFedTime, Jan 2025 |
| Jan 2025 | Iran Telecom | Root access to firewall infrastructure | $400 | CyberPress, Jan 2025 |
| Feb 2025 | US DoD contractor (EMP defense systems) | VPN access (OpenVPN/IPSec/WireGuard) | $800 | CyberPress, Feb 2025 |
| Feb 2025 | US government defense contractor (unnamed) | Firewall root access | $800 | S2W, Feb 2025 |
| Feb 2025 | Saudi Arabian call center/recruitment firm | VPN credentials | $200 | UnderCode News, Feb 2025 |
| Feb 2025 | European travel agency | SSH, full DNS, source code (4 domains) | $400 | DarkWebInformer, Feb 2025 |
| Feb 2026 | Chinese financial institutions | Firewall/network admin panels, root RCE on Linux firewalls | $300 | AhnLab ASEC, Mar 2026 |
| 2024–2025 | 16 US companies/institutions total | Various (firewall, VPN, SSH) | $200–$800 | S2W profiling, Feb 2025 |

> **Note:** S2W's profiling tool confirmed 16 US companies/institutions compromised under aliases Miyako/Minari since January 2025. Actual total victim count is likely higher given ongoing activity.

---

## Tactics, Techniques & Procedures (TTPs)

*Sourced from leaked intrusion guide (VirusTotal, Jan 2025) and Intel 471 TITAN analysis.*

### Reconnaissance
- Internet-exposed system scanning: **Shodan**, **FOFA**, **LeakIX**
- Network mapping: **Nmap**, **OWASP Amass**
- Active Directory reconnaissance: **BloodHound**

### Initial Access
- Exploitation of public-facing applications via known CVEs (see CVE list below)
- VPN misconfiguration exploitation (OpenVPN, IPSec, WireGuard)
- MFA bypass via authentication mechanism weaknesses
- Zero-day exploitation (OpenBSD zero-day documented in guide)
- Web application exploitation: **Burp Suite**

### Execution & C2
- **PowerShell** — payload download and execution (curl/wget + scripting interpreter chains)
- **Sliver** — open-source C2 framework
- **SSH** — persistent remote access; SSH tunneling for covert C2
- **Cron jobs** — scheduled task persistence on Linux
- **DNS tunneling** — covert communications

### Defense Evasion
- **Living-off-the-land binaries (LOLBins)** — blend with legitimate system activity
- **Clear Windows Event Logs** — forensic artifact removal
- **Clear bash/command history** — Linux forensic cleanup
- **NTFS File Attributes** — hiding files via alternate data streams
- **System Binary Proxy Execution** (T1218.001) — compiled HTML file execution

### Credential Access
- **NTDS dumping** — Active Directory credential extraction
- Credential theft from VPN and firewall authentication systems

### Monetization
- Lists access on BreachForums with verified proof (screenshots of domain controllers, firewall panels, etc.)
- Fixed, non-negotiable pricing model ($200–$800)
- Cryptocurrency payment for anonymity
- Sells both network access AND stolen data independently
- Published operational manual to monetize knowledge/methodology itself

---

## CVEs Actively Exploited

| CVE | Product | CVSS | Notes |
|---|---|---|---|
| CVE-2024-3400 | Palo Alto PAN-OS | 10.0 | Command injection in GlobalProtect; exploited in guide |
| CVE-2024-1709 | ConnectWise ScreenConnect | 10.0 | Authentication bypass; same CVE used by Charming Kitten day-1 |
| CVE-2024-1708 | ConnectWise ScreenConnect | 8.4 | Path traversal; companion to CVE-2024-1709 |
| CVE-2024-3094 | XZ Utils | 10.0 | Supply chain backdoor in SSH daemon |
| F5 BIG-IP AFM | F5 BIG-IP Advanced Firewall Manager | — | Specific CVE not publicly disclosed |
| Webmin | Webmin | — | Web-based system admin exploitation |
| OpenBSD | OpenBSD | — | Zero-day documented in guide; unpatched at time of writing |

---

## MITRE ATT&CK Mappings

| Technique | ID |
|---|---|
| Exploit Public-Facing Application | T1190 |
| External Remote Services | T1133 |
| Command and Scripting Interpreter: PowerShell | T1059.001 |
| Command and Scripting Interpreter: Unix Shell | T1059.004 |
| SSH | T1021.004 |
| Ingress Tool Transfer | T1105 |
| NTDS | T1003.003 |
| Indicator Removal: Clear Windows Event Logs | T1070.001 |
| Indicator Removal: Clear Command History | T1070.003 |
| NTFS File Attributes | T1564.004 |
| System Binary Proxy Execution: Compiled HTML File | T1218.001 |
| Linux and Mac File and Directory Permissions Modification | T1222.002 |
| Scheduled Task/Job: Cron | T1053.003 |
| Compromise Software Dependencies and Development Tools | T1195.001 |
| Data from Local System | T1005 |

---

## Threat Assessment

| Field | Assessment |
|---|---|
| **Threat Level** | 🔴 HIGH |
| **Sophistication** | HIGH for a solo actor — custom intrusion methodology, CVE weaponization, published training guide |
| **Volume** | HIGH — 16+ US victims in first 2 months of 2025; ongoing |
| **Multiplier Effect** | CRITICAL — Access sold to nation-states and ransomware groups; Miyako is the enabler for higher-tier threats |
| **Defense/Aerospace Relevance** | HIGH — Confirmed DoD contractor targeting; access priced for nation-state buyers |
| **Current Activity** | ACTIVE — February 2026 Chinese financial institution listings confirm ongoing operations |
| **Admiralty Grade** | C2 — Multiple independent researchers corroborate activity; individual claim verification limited |
| **TLP** | TLP:WHITE |

---

## Key Intelligence Gaps

- **Attribution:** No confirmed nationality, location, or true identity
- **Scale:** Only publicly documented sales visible; true victim count unknown
- **Buyers:** Who purchased the DoD/defense contractor access is unknown — downstream impact unassessed
- **Nation-state links:** Suspected buyers include nation-state actors but no confirmed link established

---

## Defensive Recommendations

1. **Patch CVE-2024-3400 and CVE-2024-1709 immediately** — both are CVSS 10.0 and in active use
2. **Audit all internet-facing VPN and firewall appliances** — Palo Alto PAN-OS, F5 BIG-IP, ConnectWise, Fortinet
3. **Enforce phishing-resistant MFA on VPN/remote access** — standard MFA is bypassed in documented attacks
4. **Monitor BreachForums listings** for organizational name, IP ranges, or domain references
5. **Hunt for Sliver C2 beacons** — unusual outbound TLS to non-standard ports; check for Sliver artifacts
6. **Baseline DNS traffic** — DNS tunneling is a confirmed C2 method
7. **NTDS audit** — hunt for DCSync operations or unauthorized AD replication activity
8. **Third-party/supplier security review** — Miyako exploits supply chain gaps; your vendors are your exposure

---

## Sources

- Intel 471 — mommy Access Broker Emerging Threat Report (Jun 2025): https://www.intel471.com/blog/mommy-access-broker
- Intel 471 — Threat Hunting Case Study: Detecting IAB Activity (Dec 2025): https://www.intel471.com/blog/threat-hunting-case-study-detecting-iab-activity
- Intel 471 PDF Report: https://go.intel471.com/hubfs/Emerging%20Threats/2025%20Emerging%20Threats/Emerging%20Threat%20-%20Mommy%20-%2027%20June%202025.pdf
- S2W — Weekly Darkweb February W2 (Feb 2025): https://s2w.inc/en/resource/detail/755
- DoingFedTime — China Telecom IAB Post (Jan 2025): https://doingfedtime.com/prolific-threat-actor-miyako-advertises-access-to-china-telecom-data-center/
- CyberPress — Iranian Telecom Access (Jan 2025): https://cyberpress.org/iranian-telecom-dark-web/
- CyberPress — Pentagon Contractor VPN Access (Feb 2025): https://cyberpress.org/cyber-threat-targets-pentagon/
- DarkWebInformer — European Travel Agency (Feb 2025): https://darkwebinformer.com/miyako-claims-to-be-selling-access-to-an-unidentified-european-travel-agency/
- UnderCode News — Saudi Call Center Breach (Feb 2025): https://undercodenews.com/saudi-call-center-breach-hacker-miyak0-claims-vpn-compromise/
- AhnLab ASEC — February 2026 Korean & Security Issues (Mar 2026): https://asec.ahnlab.com/en/92903/

---

*Profile created: 2026-04-08 | Last updated: 2026-04-08*
