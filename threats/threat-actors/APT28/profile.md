# 🕵️ APT28 — Threat Actor Profile

**Scoundrel #006**

---

## Identity

| Field | Details |
|---|---|
| **Primary Name** | APT28 |
| **Aliases** | Fancy Bear, Forest Blizzard, Sofacy, Sednit, Pawn Storm, STRONTIUM, FROZENLAKE, BlueDelta, Fighting Ursa, Iron Twilight, GruesomeLarch, UAC-0001, TG-4127, Tsar Team, Group 74 |
| **MITRE ATT&CK ID** | [G0007](https://attack.mitre.org/groups/G0007/) |
| **Type** | Nation-State APT |
| **Origin** | Russian Federation |
| **Sponsor** | GRU (Military Intelligence) — 85th Main Special Service Center (GTsSS), Military Unit 26165 |
| **Active Since** | 2004 |
| **Status** | 🔴 Highly Active |
| **Motivation** | Espionage · Hack-and-Leak · Influence Operations · NATO/Ukraine intelligence collection |
| **Threat Level** | 🔴 CRITICAL |
| **Admiralty Grade** | A1 (confirmed attribution by multiple governments) |
| **TLP** | TLP:WHITE |

---

## Overview

APT28 is one of the most prolific and well-documented state-sponsored threat actors on the planet. Operated by the GRU's Unit 26165, it has been active for two decades and is responsible for some of the most consequential cyberattacks in history — from the 2016 US election interference to sustained targeting of NATO defense infrastructure.

Unlike GRU Unit 74455 (Sandworm), which specializes in destructive operations, APT28's primary mission is **intelligence collection and hack-and-leak influence operations**. They steal data that matters strategically: military plans, diplomatic cables, election infrastructure, and defense logistics. Then — when it suits Moscow — they leak it.

The group has dramatically shifted tradecraft in recent years. Where they once relied heavily on custom malware, APT28 now prioritizes **cloud identity compromise** — password spraying, OAuth token theft, and mailbox delegation abuse — making detection significantly harder. They're living off the land inside Microsoft 365, and most orgs can't see them.

---

## Primary Targets

- **Defense & Military** — defense ministries, military logistics, NATO allies, weapons suppliers
- **Government** — foreign ministries, diplomatic entities, intelligence agencies
- **Transportation & Logistics** — Ukraine aid supply chains, border crossing monitoring (cameras)
- **Media & Think Tanks** — election-adjacent organizations, political campaigns, NGOs
- **Critical Infrastructure** — energy, telecom, finance adjacent to conflict zones
- **Sports & International Organizations** — WADA, OPCW, 2016 Olympics

**Geographic Focus:** Ukraine (primary post-2022), NATO member states (US, UK, Germany, France, Poland, Estonia, Romania, Czech Republic, Turkey), global when politically motivated.

---

## Signature Campaigns

| Campaign | Year | Description |
|---|---|---|
| **2016 US Election** | 2016 | DNC, DCCC, Clinton campaign hacks; data leaked via Guccifer 2.0/DCLeaks |
| **Olympic Doping Operations** | 2016–2018 | WADA, USADA, CAS hacks; leaked anti-doping records |
| **Nearest Neighbor Campaign** | 2022–2024 | Wi-Fi lateral movement attack; compromised org networks via nearby Wi-Fi |
| **Operation RoundPress** | 2023–2025 | XSS webmail exploitation (Roundcube, Horde, MDaemon, Zimbra) for mailbox data theft |
| **Ukraine Aid Logistics Campaign** | 2025 | Targeting Western logistics companies supporting Ukraine; camera surveillance of border crossings |
| **CVE-2026-21509 Maritime/Defense Wave** | Jan 2026 | 72-hour phishing blitz against defense ministries and transport operators in 9 Eastern European nations; cloud C2 via filen.io |
| **UKR.net Credential Campaign** | 2024–2025 | Automated 2FA bypass on Ukrainian webmail using compromised Ubiquiti routers |

---

## TTPs (MITRE ATT&CK)

### Initial Access
| ID | Technique |
|---|---|
| T1566.001 | Spearphishing Attachment (weaponized RTF/DOC/PDF) |
| T1566.002 | Spearphishing Link (credential harvest via fake login pages) |
| T1110.003 | Password Spraying (large-scale, Tor/VPN-routed) |
| T1190 | Exploit Public-Facing Application (Exchange, Roundcube, Cisco SNMP) |
| T1199 | Trusted Relationship (compromised government accounts used as relay) |
| T1557.004 | Adversary-in-the-Middle: Evil Twin (Wi-Fi Pineapple for credential capture) |

### Execution & Persistence
| ID | Technique |
|---|---|
| T1059.001 | PowerShell |
| T1203 | Exploitation for Client Execution (CVE-2026-21509, CVE-2023-23397) |
| T1546.015 | COM Hijacking (AUTHENTIC ANTICS, CLSID {D9144DCD-E998-4ECA-AB6A-DCD83CCBA16D}) |
| T1053.005 | Scheduled Tasks (e.g., "OneDriveHealth" task for COM hijack trigger) |
| T1098.002 | Additional Email Delegate Permissions (mailbox persistence) |

### Defense Evasion
| ID | Technique |
|---|---|
| T1027 | Obfuscated Files (XOR, RC4, base64 layering) |
| T1562.004 | Disable Firewall / Modify Rules |
| T1070.001 | Clear Windows Event Logs |
| T1036 | Masquerading (fake filenames, fake gov doc aesthetics) |

### Credential Access
| ID | Technique |
|---|---|
| T1187 | Forced Authentication (AUTHENTIC ANTICS OAuth intercept) |
| T1003.003 | OS Credential Dumping: NTDS |
| T1040 | Network Sniffing / Responder (NetBIOS Name Service poisoning) |
| T1557 | Adversary-in-the-Middle (OAuth token theft) |

### Lateral Movement & Collection
| ID | Technique |
|---|---|
| T1021.001 | Remote Desktop Protocol |
| T1021.002 | SMB/Windows Admin Shares |
| T1550.002 | Pass the Hash |
| T1092 | Air-Gap Jump via USB (Jaguar Tooth adjacent) |
| T1114.002 | Remote Email Collection (mailbox exfil via Outlook API) |

### Exfiltration
| ID | Technique |
|---|---|
| T1567.002 | Exfiltration to Cloud Storage (filen.io, Koofr, Icedrive, OneDrive) |
| T1048.003 | Exfiltration via SMTP (NotDoor Outlook backdoor) |

---

## Malware Arsenal

| Malware | Type | Notes |
|---|---|---|
| **X-Agent / Sofacy** | Modular RAT | Long-standing flagship implant; keylogger, file stealer |
| **X-Tunnel** | Encrypted tunneler | C2 traffic obfuscation |
| **CHOPSTICK** | Backdoor | HTTP/HTTPS/IMAP C2; used in DNC/DCCC ops |
| **GooseEgg** | Privilege escalation tool | Exploits CVE-2022-38028 (Windows Print Spooler); drops SYSTEM shells |
| **AUTHENTIC ANTICS** | Credential/token stealer | COM hijacking; steals Outlook OAuth tokens; exfils via Outlook API |
| **HEADLACE** | Backdoor | Diplomat-themed lure delivery; public service staging |
| **NotDoor** | Outlook VBA backdoor | 2026 active; SMTP exfiltration |
| **SimpleLoader** | Loader | XOR-encrypted multi-stage loader; CVE-2026-21509 delivery |
| **CovenantGrunt** | .NET backdoor | Modified Covenant framework; AES-256 C2 via cloud storage |
| **BeardShell** | Backdoor | Signal Messenger lure delivery; CERT-UA attributed |
| **Jaguar Tooth** | Cisco router malware | SNMP-deployed; exfil over TFTP; unauthenticated backdoor |

---

## Infrastructure Patterns

- **Cloud-as-C2**: Heavy abuse of legitimate cloud storage (filen.io, Koofr, Icedrive) for C2 — traffic looks like normal HTTPS
- **Compromised infrastructure**: Uses hacked routers (Ubiquiti EdgeOS), government email accounts, legitimate webmail providers as relay/staging
- **Fast-flux domains**: Registers new domains same-day for delivery; rapid rotation after takedowns
- **Tor/VPN routing**: All password spraying routed through Tor and commercial VPNs to obscure origin
- **Webmail provider targeting**: Exploits XSS in Roundcube, Horde, MDaemon, Zimbra to steal mailbox sessions

---

## Known IOCs

> See `iocs.md` for full indicator list.

**CVEs actively exploited:**
- `CVE-2026-21509` — Microsoft Office OLE/WebDAV security feature bypass (2026 active, KEV-listed)
- `CVE-2024-11182` — MDaemon XSS zero-day (Operation RoundPress; CISA KEV-listed)
- `CVE-2023-23397` — Microsoft Outlook NTLM credential theft (CVSS 9.8, critical)
- `CVE-2023-43770` — Roundcube XSS (Operation RoundPress)
- `CVE-2023-38831` — WinRAR code execution
- `CVE-2022-38028` — Windows Print Spooler privesc (GooseEgg)
- `CVE-2017-6742` — Cisco SNMP RCE (Jaguar Tooth)

**Delivery domains (Jan 2026 wave):**
- `wellnesscaremed[.]com` → 23.227.202[.]14
- `wellnessmedcare[.]org` → 193.187.148[.]169
- `freefoodaid[.]com` → 159.253.120[.]2
- `longsauce[.]com` → 72.62.185[.]31

**Brute force IPs (Aug 2024 advisory):**
- `70.34.253[.]247`
- `91.149.253[.]118`
- `212.127.78[.]170`

**Registry persistence (AUTHENTIC ANTICS):**
- `HKCU\Software\Microsoft\Office\16.0\Outlook\Logging\Locale` (OAuth token)
- `HKCU\Software\Microsoft\Office\16.0\Outlook\Logging\Counter` (run schedule)

---

## Geopolitical Context

APT28's operations are directly tied to GRU strategic priorities. Since Russia's 2022 full-scale invasion of Ukraine, the group has focused heavily on:

1. **Ukraine war intelligence** — targeting defense ministries, military logistics, weapons suppliers, border monitoring infrastructure
2. **NATO alliance fragmentation** — hack-and-leak operations designed to embarrass governments and create distrust
3. **Election interference** — sustained targeting of democratic processes (US 2016, France 2017, Germany 2021, potential ongoing)
4. **Sanctions evasion intel** — monitoring anti-Russia NGOs, OPCW, and international oversight bodies

APT28's "hack-and-leak" model — steal it, then weaponize it in media — makes them particularly dangerous beyond the intrusion itself. The data becomes a weapon.

---

## Connection Web

- ⛓️ **GRU Unit 74455 (Sandworm)** — Sister GRU unit; destructive operations (NotPetya, Ukraine power grid attacks). APT28 and Sandworm share infrastructure and sometimes operate in coordination. Sandworm destroys; APT28 steals.
- ⛓️ **[#004 UNC1549](https://github.com/ryansketch02-C3PO/threat-actor-repository/tree/main/threat-actors/UNC1549)** — Iranian IRGC equivalent playbook: both use fake job lures against A&D targets; geopolitically aligned on anti-NATO objectives despite being separate nation-state actors
- ⛓️ **[#003 Lazarus Group](https://github.com/ryansketch02-C3PO/threat-actor-repository/tree/main/threat-actors/Lazarus-Group)** — DPRK counterpart; all three (APT28, Lazarus, UNC1549) now run variants of the "dream job" spearphishing playbook against defense sector

---

## Defense Recommendations

1. **Patch CVE-2023-23397 immediately** — CVSS 9.8, Outlook fires on receipt, no user click needed
2. **Apply emergency Office patch for CVE-2026-21509** — used in active 2026 campaign within 24 hours of disclosure
3. **Enforce phishing-resistant MFA** — APT28's primary vector is credential theft; hardware tokens stop password spraying cold
4. **Audit mailbox delegation and OAuth consents** — hunt for suspicious delegate permissions and unexpected app consents in M365
5. **Disable legacy authentication** — NTLM relay and IMAP/POP3 attacks rely on it
6. **Monitor cloud storage traffic from endpoints** — filen.io/Koofr/Icedrive connections from non-user processes = red flag
7. **Patch webmail stacks** — Roundcube, MDaemon, Zimbra, Horde have all been exploited; if self-hosted, treat as high priority
8. **Threat hunt for COM hijacking** — specifically `{D9144DCD-E998-4ECA-AB6A-DCD83CCBA16D}` COM registration and "OneDriveHealth" scheduled tasks
9. **Block Tor exit nodes at perimeter** — password spray attempts route through Tor; block known exit node ranges
10. **Air-gap sensitive systems** — APT28 has demonstrated USB-based air-gap jumping capability

---

## References

- [MITRE ATT&CK: APT28 (G0007)](https://attack.mitre.org/groups/G0007/)
- [Trellix: CVE-2026-21509 Multi-Stage Campaign (Feb 2026)](https://www.trellix.com/blogs/research/apt28-stealthy-campaign-leveraging-cve-2026-21509-cloud-c2/)
- [Microsoft: GooseEgg — Forest Blizzard CVE-2022-38028](https://www.microsoft.com/en-us/security/blog/2024/04/22/analyzing-forest-blizzards-custom-tool-for-exploiting-cve-2022-38028-to-obtain-credentials/)
- [ESET: Operation RoundPress (webmail XSS)](https://www.welivesecurity.com/en/eset-research/operation-roundpress/)
- [NSA/CISA May 2025 Advisory: GRU Unit 26165 Targeting Western Logistics](https://www.nsa.gov/Press-Room/Press-Releases-Statements/Press-Release-View/Article/4193749/)
- [Unit 42: APT28 "Fighting Ursa" CVE-2023-23397 Exploitation](https://unit42.paloaltonetworks.com/cve-2023-23397-fighting-ursa/)
- [UK NCSC: AUTHENTIC ANTICS Malware Analysis](https://www.ncsc.gov.uk/files/Advisory-APT28-exploits-known-vulnerability.pdf)
- [DOJ 2018 Indictment: GRU Unit 26165 Officers](https://www.justice.gov/file/1080281/dl)

---

*Last Updated: 2026-04-03 | Profile by C3PO*
