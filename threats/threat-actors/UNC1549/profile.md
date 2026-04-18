# 🕵️ UNC1549 — Threat Actor Profile

**Scoundrel #004**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | UNC1549 |
| **Aliases** | Tortoiseshell, Smoke Sandstorm (formerly Bohrium), Crimson Sandstorm (formerly Curium), Imperial Kitten, TA456, Yellow Liderc, Nimbus Manticore |
| **MITRE ATT&CK ID** | No standalone ATT&CK entry (tracked as Tortoiseshell overlap) |
| **Type** | Nation-State APT |
| **Origin** | Islamic Republic of Iran |
| **Sponsor** | Islamic Revolutionary Guard Corps (IRGC) |
| **Active Since** | June 2022 (at minimum) |
| **Status** | 🔴 Highly Active |
| **Motivation** | Espionage · Intellectual property theft · Strategic intelligence for potential kinetic operations |
| **Threat Level** | 🔴 CRITICAL — Direct threat to aerospace & defense sector |

---

## Sector Targeting

| Priority | Sector |
|---|---|
| 🎯 Primary | Aerospace, Aviation & Defense |
| 🎯 Primary | Defense Industrial Base (DIB) + supply chain |
| Secondary | Government / Military adjacent |
| Secondary | Technology / IT providers with A&D clients |

**Geographic Focus:** Israel, UAE, Turkey, India, Albania (primary). Late 2023–2025 expanded to worldwide A&D supply chain. September 2025: expanded to Western Europe (Portugal, Sweden, Denmark).

---

## Background

UNC1549 is an Iranian cyber-espionage group attributed with moderate-to-high confidence to the Islamic Revolutionary Guard Corps (IRGC). Mandiant first publicly disclosed the group in February 2024 after observing sustained targeting of aerospace, aviation, and defense entities across the Middle East, with focus on Israel and UAE in the context of the Israel-Hamas conflict.

The group overlaps with **Tortoiseshell** (publicly linked to IRGC since 2019) and with **Smoke Sandstorm / Crimson Sandstorm**, indicating these are likely designations for overlapping or related IRGC-affiliated clusters operating under common tasking. Intelligence collected by this group is assessed to be of direct relevance to **strategic Iranian interests and may be leveraged to support kinetic (physical) military operations**.

Mandiant followed up with a November 2025 in-depth analysis confirming UNC1549's continued targeting of aerospace/defense with expanded and more sophisticated TTPs, including third-party supply chain exploitation, VDI breakouts, and novel custom malware families.

---

## Operations & Campaigns

### Operation "When Cats Fly" (2022–2024)
- Active since at least June 2022, still active as of February 2024 disclosure
- Targeted Israel, UAE, Turkey, India, Albania
- Deployed custom backdoors MINIBIKE and MINIBUS via job-lure spear-phishing
- Exploited Israel-Hamas war themes — fake "Bring Them Home Now" app delivering MINIBUS
- Used 125+ Microsoft Azure C2 subdomains to blend traffic with legitimate cloud services
- Fake job portals impersonating Boeing, Teledyne FLIR, DJI (drone manufacturer)

### Supply Chain Expansion (Late 2023–2025)
- Expanded globally beyond Middle East focus
- Leveraged compromised third-party vendor/supplier credentials for initial access
- VDI breakouts from Citrix/VMware/Azure Virtual Desktop environments
- September 2025: Western Europe expansion — Portugal, Sweden, Denmark
- Deployed new malware families: TWOSTROKE, DEEPROOT, GHOSTLINE, POLLBLEND, CRASHPAD, SIGHTGRAB, TRUSTTRAP/TRUSTRAP, DCSYNCER.SLICK, MiniBrowse, MiniJunk

---

## TTPs — MITRE ATT&CK Mapping

### Initial Access
| Technique | Details |
|---|---|
| T1566.001 | Spearphishing Attachment — job lure documents |
| T1566.002 | Spearphishing Link — fake Azure-hosted career portals |
| T1566.003 | Spearphishing via Service — LinkedIn, WhatsApp, social media |
| T1078 | Valid Accounts — compromised third-party credentials |
| T1199 | Trusted Relationship — pivoting from vendors/partners to primary targets |

### Execution
| Technique | Details |
|---|---|
| T1059.001 | PowerShell — recon, Kerberoasting, ping sweep |
| T1059.003 | Windows Command Shell — AD enumeration, password resets |
| T1204.002 | User Execution: Malicious File |

### Persistence
| Technique | Details |
|---|---|
| T1547.001 | Registry Run Keys / Startup Folder |
| T1053.005 | Scheduled Task — MigAutoPlay.exe sideloading setup |
| T1574.001 | DLL Search Order Hijacking — MINIBIKE, TWOSTROKE, DEEPROOT, LIGHTRAIL, GHOSTLINE, POLLBLEND, SIGHTGRAB, CRASHPAD |

### Defense Evasion
| Technique | Details |
|---|---|
| T1027 | Obfuscation — heavy compiler-level obfuscation, opaque predicates, encrypted strings |
| T1027.001 | Binary Padding — inflated file size to bypass AV scanner limits |
| T1036 | Masquerading — malicious files named as OneDrive, legitimate services |
| T1070.001 | Clear Windows Event Logs + RDP history registry deletion |
| T1553.002 | Code Signing — legit certs used on GHOSTLINE, POLLBLEND, TWOSTROKE |
| T1571 | Non-Standard Port — reverse SSH on port 443 |

### Credential Access
| Technique | Details |
|---|---|
| T1003.006 | DCSync (DCSYNCER.SLICK — Mimikatz variant) |
| T1555.003 | Browser credential theft (MiniBrowse, CRASHPAD) |
| T1056.002 | GUI Input Capture — TRUSTTRAP fake authentication popups |
| T1528 | Steal Application Access Token — RDP session hijacking |
| T1558.003 | Kerberoasting — obfuscated Invoke-Kerberoast |

### Discovery
| Technique | Details |
|---|---|
| T1087.002 | Domain Account Discovery — AD Explorer, net user/group |
| T1018 | Remote System Discovery — PowerShell ping sweeps |
| T1046 | Network Service Scanning — port scanning on admin subnets |
| T1113 | Screen Capture — SIGHTGRAB (periodic screenshots) |

### Lateral Movement
| Technique | Details |
|---|---|
| T1021.001 | RDP (primary method) |
| T1021.001 | RDP Session Hijacking — tscon to active unlocked sessions |
| T1021.006 | PowerShell Remoting |

### Command & Control
| Technique | Details |
|---|---|
| T1572 | Protocol Tunneling — reverse SSH (`ssh.exe -R`) on port 443 |
| T1071.001 | Web Protocols — HTTPS C2 via Azure Web Apps |
| T1102 | Web Service — Azure cloud infrastructure (125+ subdomains) |
| T1090 | ZEROTIER, NGROK as backup C2 channels |

### Exfiltration
| Technique | Details |
|---|---|
| T1041 | Exfiltration Over C2 Channel — HTTPS to Azure infrastructure |

---

## Custom Malware Arsenal

| Malware | Type | Notes |
|---|---|---|
| **MINIBIKE** | Backdoor | Deployed via .NET app lures; Azure C2; active since 2022 |
| **MINIBUS** | Backdoor | Deployed via fake Hamas-themed app; expanded targeting vs MINIBIKE |
| **TWOSTROKE** | C++ Backdoor | SSL/TCP-443 C2; unique victim ID via XOR of hostname; per-victim unique hashes |
| **DEEPROOT** | Backdoor | Azure C2; multiple C2 servers per binary for redundancy |
| **LIGHTRAIL** | Tunneler | Based on open-source Socks4a (Lastenzug); Azure C2 |
| **GHOSTLINE** | Backdoor | Code-signed; DLL sideloading |
| **POLLBLEND** | Backdoor | Code-signed; DLL sideloading |
| **CRASHPAD** | Credential Stealer | Browser credential extraction |
| **MiniBrowse** | Credential Stealer | Chrome + Edge credential theft via browser injection |
| **SIGHTGRAB** | Screenshot Tool | Periodic screenshots; targets privileged/sensitive users |
| **TRUSTTRAP / TRUSTRAP** | Credential Harvester | Fake auth popup windows |
| **DCSYNCER.SLICK** | DCSync Utility | Mimikatz-derived; extracts NTLM hashes from DCs |
| **MiniJunk** | Backdoor | Used in 2025 Western Europe expansion |

**Key evasion technique:** Every post-exploitation payload has a **unique hash** — even multiple samples of the same backdoor family on the same victim network. No two samples are identical.

---

## Infrastructure

- **Primary C2 platform:** Microsoft Azure Web Apps (125+ subdomains observed as of Feb 2024)
- **Azure patterns:** `active-az-*`, `intra-az-*`, `mso-internal-*`, `nxlog-*`, `skychain*`, `vm-*-svc` naming conventions
- **Additional:** ZeroTier, NGROK for persistence after victim remediation attempts
- **Fake recruitment sites:** Mimic Boeing, Airbus, Teledyne FLIR, DJI — victims receive unique login credentials as phishing vector

---

## Key Incidents & History

| Date | Event |
|---|---|
| June 2022 | UNC1549 begins targeting Israel/UAE A&D — earliest confirmed activity |
| 2023 | "Bring Them Home Now" campaign — Israel-Hamas war lures delivering MINIBUS |
| Late 2023 | Expands to worldwide A&D supply chain; third-party pivot techniques emerge |
| February 2024 | Mandiant publicly discloses UNC1549 operations ("When Cats Fly") |
| Mid-2024 onward | Mandiant responds to multiple new incident campaigns — expanded TTPs documented |
| November 2025 | Mandiant releases follow-up deep dive confirming active campaigns through 2025 |
| September 2025 | First observed expansion into Western Europe — Portugal, Sweden, Denmark targeted |

---

## Why They're Dangerous for Aerospace & Defense

1. **They specifically study your org before attacking** — role-relevant lures, internal email mimicry
2. **They anticipate investigators** — backdoors silent for months, reactivate after eradication attempts
3. **Supply chain is their bypass** — if you're hardened, they'll come through your vendors
4. **Azure C2 is nearly invisible** — hard to distinguish from legitimate Azure traffic
5. **Intelligence may feed kinetic ops** — IRGC tasking, not just espionage for espionage's sake
6. **Unique hashes per victim** — signature-based AV won't catch them

---

## Defensive Recommendations

1. **Audit third-party access** — Citrix/VMware/Azure VDI sessions with external partners are a prime entry vector
2. **MFA everywhere** — especially on admin/privileged accounts; credential theft is their core move
3. **Monitor Azure traffic** — anomalous connections to Azure subdomains with A&D-adjacent naming
4. **Block/alert on reverse SSH** — especially `ssh.exe -R` patterns on port 443
5. **EDR behavioral rules** — DLL sideloading patterns targeting Fortinet, VMware, Citrix, NVIDIA binaries
6. **Disable unused Kerberos SPN accounts** — prevents Kerberoasting
7. **Protect AD Certificate Services templates** — UNC1549 exploits misconfigured ADCS for privilege escalation
8. **Hunt for SIGHTGRAB artifacts** — scheduled screenshots saved to disk under user profile dirs
9. **Monitor `quser.exe` / `tscon`** — RDP session hijacking telltale signs
10. **Train engineers to distrust job DMs** — especially from LinkedIn regarding Boeing, Airbus, FLIR, DJI roles

---

## Connection Web

UNC1549 overlaps with:
- **Tortoiseshell** — same IRGC cluster, active since 2019, known for supply chain targeting
- **Smoke Sandstorm / BOHRIUM** — same attribution cluster (Microsoft designation)
- **Crimson Sandstorm / CURIUM / Imperial Kitten / TA456 / Yellow Liderc** — overlapping tasking and TTPs

The IRGC umbrella also connects to **Charming Kitten (APT35)** and **Handala Hack** in terms of broader Iranian state-sponsored cyber operations, though those are distinct clusters.

---

## Sources & References

- [Mandiant: "When Cats Fly" — UNC1549 Feb 2024](https://cloud.google.com/blog/topics/threat-intelligence/suspected-iranian-unc1549-targets-israel-middle-east)
- [Mandiant: Frontline Intelligence — UNC1549 TTPs Nov 2025](https://cloud.google.com/blog/topics/threat-intelligence/analysis-of-unc1549-ttps-targeting-aerospace-defense)
- [Picus Security: UNC1549 TTP Breakdown](https://www.picussecurity.com/threat-database/unc1549-ttps-iranian-apt-targeting-aerospace-and-defense)
- [MITRE ATT&CK: Tortoiseshell (G0097)](https://attack.mitre.org/groups/G0097/)

---

*Admiralty Grade: A2 | TLP:WHITE | Last updated: 2026-04-02*
