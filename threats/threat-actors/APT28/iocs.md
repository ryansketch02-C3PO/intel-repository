# 🔍 APT28 — Indicators of Compromise

> **Last Updated:** 2026-04-03
> **Admiralty Grade:** A1 (confirmed nation-state, multi-government attribution)
> **TLP:** TLP:WHITE
>
> ⚠️ APT28 rotates infrastructure aggressively. Treat all IOCs as time-bounded.
> Prioritize behavioral/TTP-based detection over static IOC matching.

---

## CVEs Actively Exploited

| CVE | CVSS | Product | Description | Status |
|---|---|---|---|---|
| CVE-2026-21509 | TBD | Microsoft Office | OLE/WebDAV security bypass; weaponized within 24h of disclosure | CISA KEV — Patch Now |
| CVE-2024-11182 | N/A | MDaemon Webmail | XSS zero-day used in Operation RoundPress; credential/session theft | CISA KEV — Patch Now |
| CVE-2023-23397 | 9.8 CRITICAL | Microsoft Outlook | NTLM hash theft on receipt; no user interaction required | CISA KEV — Patch Now |
| CVE-2023-43770 | 6.1 | Roundcube | XSS → malicious JS injection → credential exfiltration | Patch Now |
| CVE-2023-38831 | 7.8 | WinRAR | Code execution via malicious archive | Patch Now |
| CVE-2022-38028 | 7.8 | Windows Print Spooler | SYSTEM privilege escalation (GooseEgg) | Patch Now |
| CVE-2017-6742 | 8.8 | Cisco IOS SNMP | RCE via SNMP; Jaguar Tooth malware deployment | Legacy — Upgrade/Patch |
| CVE-2015-4902 | N/A | Oracle Java | Defense evasion bypass | Legacy |
| CVE-2014-4076 | N/A | Windows Kernel | Privilege escalation | Legacy |

---

## Malicious Delivery Domains (Jan 2026 Wave — Trellix)

| Domain | IP Address | Context |
|---|---|---|
| `wellnesscaremed[.]com` | 23.227.202[.]14 | CVE-2026-21509 payload delivery / WebDAV |
| `wellnessmedcare[.]org` | 193.187.148[.]169 | CVE-2026-21509 payload delivery |
| `freefoodaid[.]com` | 159.253.120[.]2 | Staging / decoy content |
| `longsauce[.]com` | 72.62.185[.]31 | Staging / decoy content |

---

## IP Addresses — Password Spray Infrastructure (Aug 2024)

| IP | Context |
|---|---|
| `70.34.253[.]247` | Brute force / password spray (Aug 2024 advisory) |
| `91.149.253[.]118` | Brute force / password spray (Aug 2024 advisory) |
| `212.127.78[.]170` | Brute force / password spray (Aug 2024 advisory) |

---

## File Hashes — Jan 2026 Campaign (Trellix/CERT-UA)

### Malicious Documents (CVE-2026-21509 lures)

| Filename | MD5 | SHA-256 |
|---|---|---|
| `BULLETEN_H.doc` | 7c396677848776f9824ebe408bbba943 | c91183175ce77360006f964841eb4048cf37cb82103f2573e262927be4c7607f |
| `Courses.doc` | 2f7b4dca1c79e525aef8da537294a6c4 | 1ed863a32372160b3a25549aad25d48d5352d9b4f58d4339408c4eea69807f50 |
| `OperInformativ_163.doc` | 41c51784f6d601ffd0e09b7d59ff6025 | b7342b03d7642c894ebad639b9b53fd851d7958298f454283c18748051946585 |
| `OperInformation.doc` | 6408276cdfd12a1d5d3ed7256bfba639 | baad1153e58c86aa1dc9346cdd06be53b5dd2a6cf76202536d6721c934008f8e |
| `1301.doc` | b6a86f44d0a3fa5a5ac979d691189f2d | 969d2776df0674a1cca0f74c2fccbc43802b4f2b62ecccecc26ed538e9565eae |
| `1291.doc` | d47261e52335b516a777da368208ee91 | fd3f13db41cd5b442fa26ba8bc0e9703ed243b3516374e3ef89be71cbf07436b |
| `International Weapons Smuggling...51.doc` | c306e0a3ec528368f0b0332104148266 | 8b0ab7f7f48bf847c3af570da7dd3e26eda9e4c4ab38c492b1b798294d7f53a5 |
| `Oper Informativ Possible International Weapons.doc` | 0df3fde016f3c0974d4aa01b06724a33 | 968756e62052f9af80934b599994addbab29f8dc2615c47cda512bae48771019 |

### Malware Binaries

| Filename | MD5 | SHA-256 | Description |
|---|---|---|---|
| `EhStoreShell.dll` | e4a5c4b205e1b80dc20d9a2fb4126d06 | a876f648991711e44a8dcf888a271880c6c930e5138f284cd6ca6128eca56ba1 | SimpleLoader (COM hijacking payload) |
| `VbaProject.OTM` | 337cecf067ecf0609b943b54fb246ed2 | 7ccf7e8050c66eed69f35159042d8043032f8afe48ae1f51fce75ce2c51395f2 | NotDoor Outlook VBA backdoor |

---

## Registry Indicators — AUTHENTIC ANTICS

| Key | Value | Purpose |
|---|---|---|
| `HKCU\Software\Microsoft\Office\16.0\Outlook\Logging\Locale` | (OAuth token data) | Stores most recently stolen OAuth 2.0 refresh token |
| `HKCU\Software\Microsoft\Office\16.0\Outlook\Logging\Counter` | (timestamp) | Next scheduled run time for token stealer |

---

## Scheduled Task Indicators — SimpleLoader Persistence

| Name | Pattern | Notes |
|---|---|---|
| `OneDriveHealth` | Triggers 60s post-registration; kills/relaunches explorer.exe | COM hijack trigger; self-deletes after execution |

---

## Malicious Archive Filenames (CVE-2023-38831 / WinRAR campaigns)

| Filename | Notes |
|---|---|
| `calc.war.zip` | WinRAR exploit delivery |
| `Roadmap.zip` | WinRAR exploit delivery |

---

## Cloud C2 Infrastructure

| Platform | Context |
|---|---|
| `filen.io` | Primary C2 for CovenantGrunt (Jan 2026 campaign) |
| `koofr.net` | Previous C2 platform (prior campaigns) |
| `icedrive.net` | Previous C2 platform (prior campaigns) |

> C2 traffic appears as normal HTTPS to cloud storage; filter by process context, not domain.

---

## AUTHENTIC ANTICS Network Indicators

| Type | Value | Notes |
|---|---|---|
| URL (abused legitimate) | `http://www.gstatic[.]com/generate_204` | Network connectivity check within stealer |

---

## Webmail Targets (Operation RoundPress)

APT28 has targeted users of the following webmail providers via XSS injection:
- Roundcube (CVE-2023-43770)
- MDaemon (CVE-2024-11182)
- Horde Webmail
- Zimbra

Organizations running self-hosted versions of these products should treat as high-priority patching targets.

---

## Detection Queries (Hunt Guidance)

**Hunt for AUTHENTIC ANTICS persistence:**
```
EventID 4657 (Registry Value Modified)
TargetObject: HKCU\Software\Microsoft\Office\16.0\Outlook\Logging\*
```

**Hunt for COM hijacking (SimpleLoader):**
```
CLSID {D9144DCD-E998-4ECA-AB6A-DCD83CCBA16D} registry modification
Process: explorer.exe loading unexpected DLL from user-writable path
Scheduled Task named: OneDriveHealth
```

**Hunt for password spray (M365):**
```
Multiple failed logins across many accounts from single IP
Login source: Tor exit node or commercial VPN provider
Authentication attempt pattern: >300/hour per account over multi-day window
```

**Hunt for cloud C2:**
```
Process making HTTPS connections to filen.io, koofr.net, icedrive.net
where process is NOT a user-initiated browser or known cloud client
```

---

## Sources

- Trellix: CVE-2026-21509 APT28 Campaign (Feb 2026) — https://www.trellix.com/blogs/research/apt28-stealthy-campaign-leveraging-cve-2026-21509-cloud-c2/
- NCSC UK: AUTHENTIC ANTICS malware analysis
- NSA/CISA Multi-agency Advisory May 2025 (GRU Unit 26165 logistics targeting)
- ESET: Operation RoundPress
- Unit 42: Fighting Ursa / CVE-2023-23397
- Microsoft: GooseEgg / Forest Blizzard CVE-2022-38028
- ThreatIntelReport.com: APT28 profile (Feb 2026)

*Last Updated: 2026-04-03 | IOCs by C3PO*
