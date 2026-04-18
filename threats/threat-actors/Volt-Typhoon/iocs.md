# Volt Typhoon — Indicators of Compromise (IOCs)

> **Last Updated:** 2026-04-05
> **Source:** CISA AA24-038A, AA23-144A, industry reporting (Secureworks, CybersecSentinel, SafeBreach)
> **TLP:** TLP:WHITE — Disclosure not limited
> **Admiralty Grade:** A1 (where sourced from CISA/Five Eyes joint advisories)

---

## ⚠️ Detection Notes

Volt Typhoon deliberately avoids leaving traditional IOCs. They use **no custom malware** for most operations, rely on **stolen valid credentials**, and route C2 through **legitimate SOHO routers** in the victim's geographic area. Traditional hash/signature-based detection will largely fail. Behavioral indicators and network anomaly detection are the primary detection surface.

**The absence of traditional IOCs is itself an IOC for Volt Typhoon.**

---

## IP Addresses (C2 / Proxy Infrastructure)

| IP Address | Notes | Source |
|---|---|---|
| 104.161.54[.]203 | Observed C2 infrastructure | CybersecSentinel 2024 |
| 109.166.39[.]139 | Observed C2 infrastructure | CybersecSentinel 2024 |
| 23.227.198[.]247 | Observed C2 infrastructure | CybersecSentinel 2024 |

> **Note:** Volt Typhoon's C2 primarily proxies through compromised SOHO routers at local ISPs — IPs rotate and belong to legitimate devices. Static IP blocking is minimally effective. Focus on behavioral detection.

---

## KV-Botnet Infrastructure

- **Device types targeted for botnet recruitment:**
  - Cisco RV320/325 (end-of-life)
  - NETGEAR ProSafe (end-of-life)
  - Various SOHO routers, firewalls, VPN appliances from multiple vendors
- **Botnet disrupted:** January 31, 2024 (FBI court-authorized operation)
- **Reconstitution rate:** ~30% of Cisco RV320/325 devices re-compromised within 37 days

---

## Network IOCs

### Fast Reverse Proxy (FRP) — C2 Ports
Volt Typhoon's custom FRP clients hardcode callbacks to the following ports:

| Port | Protocol |
|---|---|
| 8080 | TCP |
| 8443 | TCP |
| 8043 | TCP |
| 8000 | TCP |
| 10443 | TCP |

### FRP Disguise Filenames
The following filenames have been observed as renamed FRP executables:

```
cisco_up.exe
cl64.exe
vm3dservice.exe
watchdogd.exe
Win.exe
WmiPreSV.exe
WmiPrvSE.exe
```

> **Note:** These filenames are chosen to impersonate legitimate Windows/Cisco processes. Verify process hash and parent process chain before concluding legitimacy.

### Awen Webshell
- Volt Typhoon has used webshells derived from the **Awen webshell** for persistence and exfiltration `[T1505.003]`

---

## File / Hash IOCs

| Filename | SHA-256 | Notes |
|---|---|---|
| ScanLine (hacktool) | `7ff9d0...` (partial per CERT) | Network scanner observed in confirmed compromises |

> Full hash table available in CISA AA24-038A STIX file: [aa23-144a.stix_.xml](https://www.cisa.gov/sites/default/files/2023-05/aa23-144a.stix_.xml)

---

## Exploited CVEs

| CVE | CVSS | Product | Action Required |
|---|---|---|---|
| CVE-2022-42475 | 9.3 Critical | Fortinet FortiGate SSL-VPN | Patch immediately; audit SSL-VPN crash logs for buffer overflow indicators |
| CVE-2021-40539 | 9.8 Critical | ManageEngine ADSelfService Plus | Patch + audit for webshell artifacts |
| CVE-2021-27860 | 9.8 Critical | FatPipe WARP/MPVPN | Patch; check for unauthorized PortProxy entries |
| CVE-2023-27350 | 9.8 Critical | PaperCut MF/NG | Patch; audit print server access logs |

---

## Behavioral / LOTL Indicators

### High-Fidelity Behavioral Indicators (Hunt Rules)

**ntdsutil abuse:**
```
Process: ntdsutil.exe
NOT on: Domain Controller
→ HIGH PRIORITY ALERT
```

**NTDS.dit extraction via Volume Shadow Copy:**
```powershell
# Typical Volt Typhoon sequence observed:
ntdsutil "ac in ntds" "ifm" "cr fu C:\Windows\Temp\<dir>" q q
vssadmin create shadow /for=C:
```

**PortProxy registry modification:**
```
Registry path: HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp
Unusual entries → investigate source process
```

**Log clearing (critical alert):**
```
Windows Event ID 1102 — Security log cleared
Windows Event ID 104 — System log cleared
→ All occurrences must be investigated; Volt Typhoon clears logs to cover tracks
```

**Suspicious FRP / tunneling behavior:**
```
Outbound connections to ports 8080, 8443, 8043, 8000, 10443
FROM: process names matching legitimate Windows binaries
TO: external IPs not in known-good list
```

**WMIC staging behavior:**
```
Directories created by WMIC:
C:\Users\Public\pro
C:\Windows\Temp\tmp
C:\Windows\Temp\tmp\Active
→ Investigate any file staging in these paths
```

**Chrome/Edge browser credential theft:**
```
Unusual process accessing:
C:\Users\*\AppData\Local\Google\Chrome\User Data\
C:\Users\*\AppData\Local\Microsoft\Edge\User Data\
FROM: non-browser process → HIGH PRIORITY ALERT
```

**Mimikatz / Impacket indicators:**
```
LSASS memory reads from unexpected processes
Kerberos TGT requests from service accounts outside normal hours
SMB relay activity between hosts not normally communicating
```

---

## Recommended Hunt Queries (Windows Event Logs)

```
# Hunt for ntdsutil on non-DCs
Event ID 4688 (Process Creation)
CommandLine contains: ntdsutil

# Hunt for PortProxy changes
Event ID 4657 (Registry value modified)
ObjectName contains: PortProxy

# Hunt for security log clearing
Event ID 1102 (Security audit log cleared)
Event ID 104 (System log cleared)

# Hunt for PSExec usage
Event ID 7045 (New service installed)
ServiceFileName contains: PSEXESVC

# Hunt for WMIC remote execution
Event ID 4688
Process: wmiprvse.exe spawning cmd.exe or PowerShell
```

---

## Infrastructure Indicators (YARA / Detection Rules)

CISA AA24-038A includes official YARA rules — reference the full advisory for implementation:
- **Advisory:** https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-038a
- **STIX feed:** https://www.cisa.gov/sites/default/files/2023-05/aa23-144a.stix_.xml

---

## References

- CISA AA24-038A (Feb 2024): https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-038a
- CISA AA23-144A (May 2023): https://media.defense.gov/2023/May/24/2003229517/-1/-1/0/CSA_Living_off_the_Land.PDF
- DOJ KV-Botnet press release: https://www.justice.gov/opa/pr/us-government-disrupts-botnet-peoples-republic-china-used-conceal-hacking-critical
- MITRE ATT&CK G1017: https://attack.mitre.org/groups/G1017/
