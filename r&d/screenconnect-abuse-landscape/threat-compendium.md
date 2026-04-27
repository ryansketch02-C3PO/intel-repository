# ScreenConnect Abuse Landscape — Campaign Threat Compendium

**Maintained by:** C3PO Threat Intelligence
**Last Updated:** 2026-04-27
**Classification:** TLP:WHITE

---

## Overview

ConnectWise ScreenConnect has become one of the most heavily abused remote access tools in the threat landscape. Huntress data indicates **74.5% of all abused RMM tools involve ScreenConnect** — ahead of RDP, LogMeIn, TeamViewer, Atera, and VNC combined. Its status as a legitimate, signed, vendor-trusted application makes it near-invisible to AV/EDR without behavioral monitoring.

Abuse takes two distinct forms:
1. **Weaponized delivery** — Trojanized or rogue ScreenConnect installers delivered via phishing or malvertising to gain initial access
2. **Platform exploitation** — Vulnerabilities in the ScreenConnect server itself (CVE-2025-3935, CVE-2026-3564) allowing session hijacking or code injection

This document consolidates four distinct active threat threads sharing ScreenConnect as a common denominator.

---

## Thread 1 — Tax Season Malvertising + HwAudKiller EDR Killer

**Source:** Huntress (Anna Pham), March 24, 2026
**Active Period:** January 2026 – present (ongoing as of reporting)
**Attribution:** Unknown — Russian-language comments in attacker JavaScript suggest Russian-speaking developer
**Assessed Intent:** Pre-ransomware staging or initial access brokering

### Summary

Large-scale Google Ads poisoning campaign targeting U.S. users searching for tax-related documents. Rogue ScreenConnect installers deliver a BYOVD EDR killer (HwAudKiller) capable of terminating Microsoft Defender, Kaspersky, and SentinelOne at kernel level — followed by credential dumping and lateral movement preparation.

### Attack Chain

```
User searches: "W2 tax form" / "W-9 Tax Forms 2026"
        ↓
Google Ads → sponsored results → malicious landing page
        ↓
Double-cloaking layer:
  - JustCloakIt (server-side filtering) runs first
  - Adspect (client-side JavaScript fingerprinting) runs second
  - Security scanners receive benign page
  - Real victims receive payload
        ↓
Download: rogue ScreenConnect installer
        ↓
ScreenConnect establishes C2 session to attacker
        ↓
Multi-stage crypter deployed:
  - Allocates 2GB of memory, fills with zeros, frees it
    → causes AV emulators to fail (resource exhaustion)
        ↓
HwAudKiller deployed (BYOVD):
  - Drops HWAuidoOs2Ec.sys (legitimate signed Huawei audio driver)
  - Windows loads without complaint (DSE not triggered)
  - Driver terminates AV/EDR processes from kernel mode
  - Kills: Microsoft Defender, Kaspersky, SentinelOne
        ↓
Post-EDR-kill:
  - LSASS credential dump
  - NetExec for network reconnaissance and lateral movement
  - FleetDeck Agent deployed for RMM redundancy
  - Additional ScreenConnect trial instances deployed (2–3 per host)
```

### Key Technical Details

| Component | Details |
|---|---|
| Malvertising network | Google Ads — tax-themed lures |
| Cloaking Layer 1 | JustCloakIt (JCI) — server-side user-agent/IP filtering |
| Cloaking Layer 2 | Adspect — client-side JavaScript fingerprint |
| Landing page | PHP-based Traffic Distribution System |
| Delivery domain (observed) | `bringetax[.]com/humu/` |
| EDR killer | HwAudKiller |
| Vulnerable driver | `HWAuidoOs2Ec.sys` — Huawei laptop audio driver (legitimately signed) |
| Technique | BYOVD (Bring Your Own Vulnerable Driver) |
| Targets killed | Microsoft Defender, Kaspersky, SentinelOne |
| Redundancy tool | FleetDeck Agent |
| Credential theft | LSASS dump |
| Lateral movement | NetExec |
| Attacker infrastructure clue | Russian-language JavaScript comments in open directory |

### IOCs

| Indicator | Type |
|---|---|
| `bringetax[.]com` | Malicious domain (observed) |
| `HWAuidoOs2Ec.sys` | Malicious/abused driver |
| HwAudKiller (multiple hashes — see Huntress report) | Malware binary |
| Multiple ScreenConnect trial sessions on single host within hours | Behavioral IOC |
| FleetDeck Agent + ScreenConnect co-deployment | Behavioral IOC |
| 2GB memory allocation followed by immediate free in crypter | AV evasion behavior |

---

## Thread 2 — Trojanized ClickOnce Installers + Multi-RAT Deployment

**Source:** Acronis TRU (Ilia Dafchev, Eliad Kimhy), September 2025
**Active Period:** March 2025 – present (ongoing)
**Attribution:** Unknown — no definitive attribution; shared infrastructure patterns suggest possible access-as-a-service model
**Assessed Intent:** Espionage, credential theft, potential ransomware staging

### Summary

Ongoing campaign delivering trojanized ScreenConnect installers via social engineering lures (financial documents, legal agreements, Social Security statements). Attackers evolved from full installers to ClickOnce runner installers — eliminating embedded configuration and reducing static detection opportunities. Upon connection, AsyncRAT and a custom PowerShell-based RAT are auto-deployed simultaneously.

### Attack Chain

```
Social engineering lure (phishing or drive-by):
  - "agreement_support-pdf.Client.exe"
  - Social Security statement lures
  - Invoice / financial document lures
        ↓
ClickOnce runner installer executes:
  - No embedded config — fetches components at runtime
  - Reduces static detection opportunities
        ↓
ScreenConnect session established → attacker-controlled relay
        ↓
Automated dual-RAT deployment (within minutes):
  ├── AsyncRAT (well-known, open-source RAT)
  └── Custom PowerShell RAT (novel — not in open-source repos)
        ↓
Custom RAT behavior:
  - System reconnaissance
  - Data exfiltration via Microsoft.XMLHTTP
  - Multiple obfuscation layers
        ↓
2 weeks post-compromise — chain upgrade:
  - Batch + VBS loaders replace direct RAT execution
  - Fetch and execute encoded .NET assemblies in memory
  - AsyncRAT redeployed via new chain
        ↓
Optional: PureHVNC RAT (hidden VNC — third payload in some chains)
```

### Infrastructure Pattern

Attackers reuse preconfigured Windows Server 2022 VMs with consistent hostnames:
- `WIN-BUNS25TD77J`
- `COPY-OF-VM-2022`

Enables rapid redeployment after infrastructure takedown. Consistent across multiple campaign waves — suggests single operator or shared infrastructure provider.

### Key Technical Details

| Component | Details |
|---|---|
| Installer type | ClickOnce runner (evolved from full installer) |
| Observed lure filename | `agreement_support-pdf.Client.exe` |
| RAT 1 | AsyncRAT (known) |
| RAT 2 | Custom PowerShell RAT (novel — homemade) |
| RAT 3 | PureHVNC (hidden VNC — observed in subset) |
| Evolution at T+2 weeks | Batch/VBS loaders + encoded .NET assemblies |
| Infrastructure | Recycled Windows Server 2022 VMs, consistent hostnames |
| Overlap with other malware | XWorm, DCRat distribution infrastructure |
| Primary victim geography | United States |

---

## Thread 3 — STAC6405 / Phishing-to-RMM Stacking

**Source:** Sophos MDR, November 2025; Red Canary (independent confirmation)
**Active Period:** April 2025 – at least November 2025 (phishing links active at time of reporting)
**Sophos Tracking:** STAC6405
**Assessed Intent:** Initial access brokering; infostealer deployment confirmed in subset

### Summary

Phishing campaign delivering RMM tools (LogMeIn Resolve and ScreenConnect) via invitation/RSVP-style lures, primarily targeting U.S. organizations. 80+ organizations confirmed affected across multiple sectors. Post-access activity includes infostealer deployment and RMM stacking (using existing ScreenConnect installations to blend with legitimate traffic).

### Attack Chain

```
Phishing email: invitation / RSVP / meeting lure
        ↓
Victim downloads RMM binary (LogMeIn Resolve or ScreenConnect)
  - User-agent filtering: redirects non-Windows/non-Android visitors to error page
        ↓
RMM establishes attacker access
        ↓
Attacker enumerates host — identifies existing ScreenConnect if present
        ↓
Two observed post-access branches:

Branch A (pre-existing ScreenConnect):
  - Uses existing ScreenConnect installation (blends with legitimate IT activity)
  - Downloads 8776_6713_exe.zip via ScreenConnect session
  - Likely infostealer payload

Branch B (fresh ScreenConnect install):
  - invite.exe launches new ScreenConnect client as service
  - Connects to: relay[.]aceheritagehouse[.]top:8041
  - Also launches Java-based RAT (RemoteAccess.jar + jwrapper_utils.jar)
  - Enumerates firewall rules
  - Registers simplegateway.service via SimpleService.exe (JWrapper abuse)
```

### Key Technical Details

| Component | Details |
|---|---|
| Lure type | Invitation / RSVP / meeting notifications |
| Primary RMM | LogMeIn Resolve (formerly GoToResolve); ScreenConnect as secondary |
| Observed C2 relay | `relay[.]aceheritagehouse[.]top:8041` |
| Observed dropper | `invite.exe` |
| Java RAT | `RemoteAccess.jar`, `jwrapper_utils.jar` |
| Persistence | `simplegateway.service` via JWrapper's `SimpleService.exe` |
| Scale | 80+ organizations confirmed |
| Geography | Primarily United States, multiple sectors |

---

## Thread 4 — Platform Vulnerabilities (CVE-2025-3935 + CVE-2026-3564)

These are vulnerabilities in the ScreenConnect platform itself — distinct from campaigns using ScreenConnect as a delivery tool.

### CVE-2025-3935 — ViewState Code Injection (Machine Key Exposure)

| Field | Details |
|---|---|
| **CVE** | CVE-2025-3935 |
| **Type** | ViewState code injection via exposed ASP.NET machine keys |
| **Severity** | High |
| **Patch** | Patched May 2025 |
| **CISA KEV** | Yes — added June 2025 |
| **Attribution** | Nation-state actor (intelligence collection, not ransomware) — ConnectWise + Mandiant investigation |

ConnectWise cloud infrastructure was breached. Nation-state actor (unattributed — intelligence collection focus) exploited exposed machine key material to perform ViewState injection attacks, achieving RCE on ScreenConnect instances. Small number of customers affected; Mandiant engaged for remediation. CISA added to KEV catalog June 2025.

---

### CVE-2026-3564 — ASP.NET Machine Key Extraction → Session Hijacking

| Field | Details |
|---|---|
| **CVE** | CVE-2026-3564 |
| **Type** | Improper verification of cryptographic signature — ASP.NET machine key extraction |
| **CWE** | CWE-347 |
| **CVSS** | 9.0 (AV:N/AC:H/PR:N/UI:N/S:C/C:H/I:H/A:H) |
| **Affected** | ScreenConnect < 26.1 |
| **Patch** | ScreenConnect 26.1 (March 17, 2026) |
| **Wild Exploitation** | Exploitation attempts observed (no confirmed successful exploitation of ConnectWise-hosted instances) |

Earlier versions of ScreenConnect stored unique machine keys in server configuration files. Under certain conditions (server file access, backup exposure, historical snapshot access), these keys could be extracted and used to forge authentication sessions. Post-session-hijack: attacker can open remote sessions to managed endpoints, run commands, install malware.

**Recommended remediation:** Upgrade to 26.1 AND rotate cryptographic material (new key generation option added in 26.1).

---

## Cross-Campaign Patterns

| Pattern | Threads |
|---|---|
| Stacking multiple RMM tools for redundancy | Thread 1, Thread 2, Thread 3 |
| ClickOnce / signed installer for AV evasion | Thread 2 |
| BYOVD EDR termination before post-exploitation | Thread 1 |
| Java-based RAT alongside RMM | Thread 3 |
| Social engineering lures mimicking trusted documents | Thread 2, Thread 3 |
| Rapid infrastructure rotation / VM reuse | Thread 2 |
| Exploitation of platform-level vulnerabilities | Thread 4 |
| Nation-state interest in ScreenConnect infrastructure | Thread 4 |

---

## Why ScreenConnect?

- **Legitimacy:** Signed by a known vendor — trusted by Windows, AV, and EDR by default
- **Ubiquity:** Deployed by MSPs, IT departments, and enterprises globally — blends with legitimate traffic
- **Trial tier:** Free trial instances require no payment — attackers operate at zero cost
- **Automation features:** Built-in scripting and file transfer remove need for separate tooling
- **Attacker economics:** Huntress data — 74.5% of all RMM tool abuse involves ScreenConnect

---

## Detection Guidance

### High-Priority Behavioral Indicators

| Indicator | Notes |
|---|---|
| ScreenConnect deployed outside IT change management window | Most significant single indicator |
| Multiple ScreenConnect trial instances on a single host within hours | Thread 1 signature |
| ScreenConnect session followed by LSASS access within same session | Thread 1 — credential theft |
| ScreenConnect deploying secondary RMM tool (FleetDeck, LogMeIn, etc.) | Redundancy stacking |
| HWAuidoOs2Ec.sys loaded (Huawei audio driver) | BYOVD indicator |
| svc_samcore.log in /opt/cisco/platform/logs/var/log/ | FIRESTARTER (separate but related) |
| ScreenConnect binary with unusual parent process (not update/installer) | Trojanized installer |
| ASP.NET ViewState traffic to ScreenConnect server with anomalous structure | CVE-2025-3935 / CVE-2026-3564 |

### For MSPs / ScreenConnect Operators

- Audit all active ScreenConnect sessions — identify any unauthorized relay connections
- Review server-level access logs for unexpected access to configuration files or backups
- Upgrade to ScreenConnect 26.1 immediately — rotate cryptographic material post-upgrade
- Enable enhanced logging per ConnectWise guidance
- Limit backup, config export, and snapshot access to trusted users and systems

---

## Disclosure Timeline (Combined)

| Date | Event |
|---|---|
| March 2025 | Acronis TRU first observes trojanized ClickOnce installer campaigns |
| April 2025 | Sophos STAC6405 earliest observed activity |
| May 2025 | CVE-2025-3935 patched; ConnectWise breach confirmed (Mandiant) |
| June 2025 | CISA adds CVE-2025-3935 to KEV catalog |
| September 2025 | Acronis TRU publishes trojanized installer research |
| October–November 2025 | STAC6405 peak activity (80+ orgs) |
| January 2026 | Tax season malvertising campaign begins (Huntress) |
| March 17, 2026 | CVE-2026-3564 disclosed; ScreenConnect 26.1 released |
| March 24, 2026 | Huntress publishes HwAudKiller/tax malvertising analysis |
| April 27, 2026 | This compendium published |

---

## References

- [Huntress — Tax Search Ads Deliver ScreenConnect Malware Using Huawei Driver](https://thehackernews.com/2026/03/tax-search-ads-deliver-screenconnect.html)
- [Acronis TRU — Trojanized ScreenConnect Installers Evolve](https://www.acronis.com/en/tru/posts/trojanized-screenconnect-installers-evolve-dropping-multiple-rats-on-a-single-machine/)
- [Sophos — STAC6405: Incident Responders, S'il Vous Plait](https://www.sophos.com/en-us/blog/incident-responders-s-il-vous-plait)
- [Help Net Security — Unpatched ScreenConnect Servers Open to Attack (CVE-2026-3564)](https://www.helpnetsecurity.com/2026/03/20/connectwise-screenconnect-cve-2026-3564/)
- [ConnectWise Security Bulletin — ScreenConnect 26.1](https://www.connectwise.com/company/trust/security-bulletins/2026-03-17-screenconnect-bulletin)
- [CRN — ConnectWise ScreenConnect Vulnerability Exploited: CISA (CVE-2025-3935)](https://www.crn.com/news/security/2025/connectwise-screenconnect-vulnerability-exploited-cisa)
