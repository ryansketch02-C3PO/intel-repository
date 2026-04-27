# FIRESTARTER — Cisco ASA/FTD Patch-Resistant Backdoor

## Identity

| Field | Details |
|---|---|
| **Malware Name** | FIRESTARTER |
| **Family** | LineRunner |
| **Type** | Persistent Backdoor / Shellcode Loader / C2 Implant |
| **Platform** | Cisco ASA and Firepower Threat Defense (FTD) — Linux ELF binary |
| **Attributed To** | UAT-4356 / Storm-1849 (China-linked, state-sponsored) |
| **Campaign** | ArcaneDoor (2024, ongoing evolution) |
| **Companion Tool** | LINE VIPER (post-exploitation toolkit) |
| **Related Malware** | RayInitiator (bootkit — significant technical overlap) |
| **Disclosed** | April 23–24, 2026 — CISA + UK NCSC joint malware analysis report |
| **Cisco Attribution** | Cisco Talos — UAT-4356 advisory published April 23, 2026 |
| **Confirmed Victims** | 1+ U.S. Federal Civilian Executive Branch (FCEB) agencies |
| **Campaign Scope** | "Widespread" — multiple targets assessed |
| **Active Since** | No later than September 2025 |
| **Threat Level** | CRITICAL |
| **Admiralty Grade** | A1 — confirmed by CISA, NCSC, Cisco Talos, active exploitation verified |

---

## Overview

FIRESTARTER is a purpose-built, state-sponsored backdoor targeting Cisco ASA and Firepower Threat Defense (FTD) appliances. It is not a commodity implant — it is a precision weapon engineered to survive every standard remediation action an enterprise operations team would take. **Patching, rebooting, firmware updates, and CLI reload commands do not remove it.** The only confirmed removal method is a physical hard power cycle — unplugging the device from its power supply.

CISA confirmed FIRESTARTER on a U.S. federal civilian agency's Cisco Firepower device in September 2025. The backdoor persisted through all subsequent patches and remediation, and was still being used to redeploy the LINE VIPER post-exploitation toolkit **in March 2026 — nearly six months after initial compromise**.

FIRESTARTER is attributed to **UAT-4356** (also tracked as Storm-1849), the same China-linked state-sponsored actor behind the 2024 **ArcaneDoor** campaign that exploited Cisco ASA zero-days for network perimeter espionage. FIRESTARTER is not an evolution of ArcaneDoor — it is a refinement, demonstrating sustained investment in purpose-built, long-dwell network perimeter implants.

CISA updated Emergency Directive 25-03 on April 23, 2026, requiring all FCEB agencies to audit Cisco firewall infrastructure and submit device core dumps by April 24, with hard resets mandated by April 30.

---

## Attack Chain

### Stage 1 — Initial Access

| CVE | Type | CVSS | Description |
|---|---|---|---|
| CVE-2025-20333 | Missing Authorization (CWE-862) | 9.9 | Authenticated RCE in Cisco ASA/FTD VPN web server via crafted HTTP requests — root execution |
| CVE-2025-20362 | Buffer Overflow (CWE-120) | 6.5 | Unauthenticated access to restricted URL endpoints in ASA/FTD |

Both vulnerabilities were patched by Cisco in September 2025. Devices compromised **before** patching retain FIRESTARTER regardless of patch status.

### Stage 2 — LINE VIPER (Post-Exploitation Toolkit)

LINE VIPER is deployed first to establish deep access and harvest intelligence:

| Capability | Notes |
|---|---|
| Execute arbitrary CLI commands | Full device control |
| Perform live packet captures | Intercepts network traffic in transit |
| Bypass VPN AAA authentication | Establishes actor-controlled VPN sessions using dormant/legitimate accounts |
| Suppress syslog messages | Hides all malicious activity from logging |
| Harvest user CLI command history | Credential and operational intelligence |
| Force delayed device reboot | Operational control of device lifecycle |
| Harvest credentials, certificates, private keys | Persistent authentication material |

### Stage 3 — FIRESTARTER Deployment (Persistence Layer)

FIRESTARTER is dropped as a Linux ELF binary and injects itself into the **LINA process** — Cisco's core network processing and security engine for ASA/FTD.

**LINA hook installation:**
- Replaces a pre-defined WebVPN XML handler function in LINA's memory with a malicious Stage 2 shellcode handler
- Normal WebVPN traffic is forwarded to the legitimate handler — FIRESTARTER is invisible to standard traffic analysis
- When a request containing **magic bytes** (specific trigger sequence) arrives, FIRESTARTER activates and executes attacker-supplied shellcode directly in LINA's memory space
- Shellcode delivery enables: deployment of LINE VIPER, credential harvest, recon, lateral pivot

### Stage 4 — Persistence via CSP_MOUNT_LIST

```
FIRESTARTER modifies CSP_MOUNT_LIST
(Cisco Service Platform boot/mount configuration)
       ↓
Backup copy written to:
/opt/cisco/platform/logs/var/log/svc_samcore.log
       ↓
On reboot or firmware update:
FIRESTARTER copies itself to /usr/bin/lina_cs
Restores CSP_MOUNT_LIST to remove trojanized copy
       ↓
lina_cs auto-launches via modified boot sequence
FIRESTARTER reinjects into LINA process
       ↓
Signal handlers intercept OS termination signals
Reinstallation executes before process can be killed
```

**Why CLI commands fail:**
- `shutdown`, `reboot`, `reload` — all trigger signal handlers → FIRESTARTER reinstalls before process exits
- Firmware updates — FIRESTARTER restores itself during boot sequence
- Only a **cold restart (physical power disconnect)** bypasses the signal handler and prevents reinstallation

### Stage 5 — C2 via Disguised WebVPN Traffic

FIRESTARTER communicates via **custom-prefixed WebVPN XML requests** — blending C2 traffic into the legitimate SSL VPN traffic the compromised device handles by design. No anomalous network signatures appear under standard monitoring.

---

## Covert Infrastructure

UAT-4356 routes traffic through a **botnet of compromised SOHO routers and IoT devices** (overlapping with Volt Typhoon and Flax Typhoon infrastructure). Traffic passes through multiple traversal nodes before exiting near the target's geographic region — complicating attribution and evading geo-based filtering.

---

## Affected Hardware

| Series | Models |
|---|---|
| Cisco Firepower | 1000, 2100, 4100, 9300 series |
| Cisco Secure Firewall | 200, 1200, 3100, 4200, 6100 series |
| Software | Cisco ASA software, Cisco FTD software |

---

## IOCs & Detection

> CRITICAL: Do NOT reboot, patch, run reload, or make configuration changes before forensic collection — volatile artifacts will be destroyed while FIRESTARTER remains.

### File System Indicators

| Path | Description |
|---|---|
| `/usr/bin/lina_cs` | FIRESTARTER primary binary — malicious process |
| `/opt/cisco/platform/logs/var/log/svc_samcore.log` | Backup/persistence copy |

### Process Indicators

```bash
# Detection command — run on suspect device
show kernel process | include lina_cs

# If lina_cs appears in output: device is compromised
```

### Signature-Based Detection

| Signature | Type | Notes |
|---|---|---|
| `Unix.Malware.Generic-10059965-0` | ClamAV | Detects FIRESTARTER binary |
| Snort Rule 62949 | Network | Exploitation activity |
| Snort Rule 65340 | Network | CVE-2025-20333 payload |
| Snort Rule 46897 | Network | CVE-2025-20362 payload |
| CISA + NCSC YARA rules | File/Memory | Published in joint MAR — apply to core dumps and disk images |

### Network Indicators

| Indicator | Description |
|---|---|
| Unusual WebVPN XML authentication requests with non-standard prefixes | FIRESTARTER C2 trigger traffic |
| VPN sessions established from dormant or inactive user accounts | LINE VIPER AAA bypass activity |
| Absence of syslog entries during suspicious activity windows | LINE VIPER syslog suppression |
| Outbound connections to SOHO router IP ranges | Covert traversal network egress |

### MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploit Public-Facing Application | T1190 | CVE-2025-20333/20362 initial access |
| Implant Internal Image | T1601.001 | Modifying Cisco firmware/boot sequence via CSP_MOUNT_LIST |
| Modify System Image | T1601 | LINA process injection; WebVPN handler replacement |
| Traffic Signaling: Magic Bytes | T1205.002 | Magic-byte-triggered C2 activation in WebVPN stream |
| Boot or Logon Autostart Execution | T1037 | CSP_MOUNT_LIST persistence mechanism |
| Valid Accounts | T1078 | LINE VIPER VPN sessions via dormant accounts |
| Indicator Removal: Disable Windows Event Logging | T1562.002 | LINE VIPER syslog suppression |
| Network Sniffing | T1040 | LINE VIPER packet capture capability |
| Credential Access from Network Device CLI | T1552.004 | Harvesting CLI history, credentials, certificates, keys |
| Proxy: Multi-hop Proxy | T1090.003 | SOHO router botnet traversal network |

---

## Incident Response

### Forensic Collection First (before any remediation)

1. **DO NOT** reboot, reload, patch, or modify the device — forensic artifacts will be lost
2. Collect a device core dump
3. Upload core dump to CISA Malware Next-Gen analysis portal (US organizations)
4. Apply CISA/NCSC YARA rules to core dump for FIRESTARTER detection
5. Run: `show kernel process | include lina_cs` — presence confirms compromise
6. **Treat all device configuration as untrusted:** rotate all credentials, certificates, and private keys immediately

### Removal

| Method | Effect |
|---|---|
| Hard power cycle (unplug physical power) | Removes in-memory FIRESTARTER implant temporarily |
| `reload`, `reboot`, `shutdown` CLI commands | Does NOT remove FIRESTARTER — signal handlers prevent it |
| Firmware update | Does NOT remove FIRESTARTER — restores itself during boot |
| **Full reimage using Cisco fixed releases** | ONLY permanent remediation — required by Cisco and CISA |

### For FTD devices not in lockdown mode (coordinate with CISA first):
```bash
sudo kill -9 $(pidof lina_cs)
# Then reload device
# DO NOT perform without CISA coordination — preserves forensic artifacts
```

---

## Strategic Context

FIRESTARTER represents the maturation of a targeting philosophy that has defined advanced Chinese espionage operations since at least 2024: **compromise the network perimeter device, not the endpoint.** Perimeter appliances are trusted, opaque, rarely monitored at the process level, and sit at the intersection of all encrypted network traffic.

UAT-4356's design choices reflect deep operational tradecraft:
- **LINA injection** — embeds inside the one process that must stay running for the device to function
- **WebVPN XML C2** — hides in traffic the device processes by design
- **CSP_MOUNT_LIST persistence** — exploits the exact mechanism defenders rely on for remediation
- **Signal handler reinstallation** — defeats every CLI command a network operator would run

This is the same strategic logic as APT28's DNS hijacking for OAuth token theft, Volt Typhoon's SOHO router botnet, and Salt Typhoon's telecom backbone compromise. The target is not the user's machine — it is the trusted infrastructure between users and the internet.

---

## Disclosure Timeline

| Date | Event |
|---|---|
| May 2024 | ArcaneDoor disclosed — UAT-4356 exploits Cisco ASA zero-days |
| September 2025 | CISA ED 25-03 issued — patches released for CVE-2025-20333/20362 |
| September 2025 | FIRESTARTER deployed on federal agency Cisco Firepower device (before patching) |
| November 2025 | CISA updates ED 25-03 guidance with additional mitigations |
| March 2026 | UAT-4356 uses persistent FIRESTARTER to redeploy LINE VIPER — 6 months post-initial-compromise |
| April 23, 2026 | CISA + NCSC joint MAR published; Cisco Talos advisory; ED 25-03 updated again |
| April 24, 2026 | Federal agency deadline: device audit + core dump submission |
| April 30, 2026 | Federal agency deadline: hard-reset all potentially affected Cisco firewall devices |

---

## References

- [CISA + NCSC Joint Malware Analysis Report — FIRESTARTER](https://www.cisa.gov/resources-tools/resources/malware-analysis-report-firestarter)
- [Cisco Talos — UAT-4356 and FIRESTARTER Advisory](https://blog.talosintelligence.com/uat4356-firestarter-cisco-asa-ftd/)
- [The Hacker News — FIRESTARTER Backdoor Hit Federal Cisco Firepower Device](https://thehackernews.com/2026/04/firestarter-backdoor-hit-federal-cisco.html)
- [CyberScoop — US, UK Agencies Warn Hackers Were Hiding on Cisco Firewalls](https://cyberscoop.com/cisco-firestarter-malware-cisa-warning/)
- [SecurityWeek — US Federal Agency Cisco Firewall Infected With FIRESTARTER](https://www.securityweek.com/us-federal-agencys-cisco-firewall-infected-with-firestarter-backdoor/)
- [TechGines — FIRESTARTER Cisco Firepower Backdoor: The Malware That Survives Patching](https://www.techgines.com/post/firestarter-cisco-firepower-backdoor-cisa-warning-2026)
- [ISSSource — Firestarter Malware Analysis from Feds](https://www.isssource.com/firestarter-malware-analysis-from-feds/)
- [CISA Emergency Directive 25-03 (updated)](https://www.cisa.gov/emergency-directive-25-03)
