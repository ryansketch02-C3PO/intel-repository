# BlackCat / ALPHV — Indicators of Compromise

> **Scoundrel #020** | C3PO Threat Actor Repository  
> Source: FBI FLASH CU-000167-MW (Apr 2022), CISA AA23-353A (Dec 2023 / updated Feb 2024)  
> TLP: CLEAR

---

## ⚠️ Usage Note

These IOCs span 2021–2024. Many hashes and IPs will be stale for blocking purposes but remain valuable for **threat hunting in historical logs**. Always cross-reference against current threat intel feeds before using for active defense.

---

## File Hashes

### BlackCat/ALPHV Ransomware Binaries (SHA-256)

```
731adcf2d7fb61a8335e23dbee2436249e5d5753977ec465754c6b699e9bf161
f837f1cd60e9941aa60f7be50a8f2aaaac380f560db8ee001408f35c1b7a97cb
9f2309285e8a8471fce7330fcade8619d94d4a63e5b2060f1c1fd1a390c1cdff
```

### PowerShell Scripts (MD5)

| Filename | MD5 Hash |
|---|---|
| `amd - Copy.ps1` | `861738dd15eb7fb50568f0e39a69e107` |
| `ipscan.ps1` | *(hash varies by campaign)* |
| `Runs1.ps1` (disables McAfee) | *(hash varies by campaign)* |

### Batch Scripts (MD5)

| Filename | MD5 | Purpose |
|---|---|---|
| `CheckVuln.bat` | `f5ef5142f044b94ac5010fd883c09aa7` | Vulnerability enumeration |
| `Create-share-RunAsAdmin.bat` | `84e3b5fe3863d25bb72e25b10760e861` | Share creation |
| `start.bat` | *(varies)* | Launch ransomware with arguments |
| `est.bat` | *(varies)* | Copy ransomware to other locations |
| `drag-and-drop-target.bat` | *(varies)* | Target MySQL server |
| `run.bat` | *(varies)* | SSH callout to external C2 |

---

## Tool Hashes (Known Malicious Variants)

| Filename | Notes |
|---|---|
| `Mim.exe` / `xxx.exe` / `xxxw.exe` | Mimikatz variants |
| `crackmapexec.exe` | Network exploitation tool |
| `plink.exe` | SSH tunneling (Putty Link) |
| `PsExec64.exe` | Lateral movement |
| `Services.exe` | Masqueraded service binary |

---

## File System Artifacts

### Ransom Note
- **Filename pattern:** `RECOVER-XXXXXXX-FILES.txt` (7-digit alphanumeric extension)
- **Legacy filename:** `file.txt`

### Malicious User Account
- **Username:** `aadmin` — created for Kerberos token generation and domain persistence

### File Extension
- Encrypted files appended with a unique random extension (varies per victim/campaign)

---

## Network Indicators

### C2 / Communication Channels
| Type | Indicator |
|---|---|
| Protocol | TOR (.onion) — victim-specific negotiation portals |
| Protocol | Tox (encrypted messaging) |
| Protocol | HTTPS to Mega.nz, Dropbox (exfil staging) |
| Protocol | SSH reverse tunnels (Plink/Ngrok for C2 relay) |

> ⚠️ Specific TOR onion addresses and IP infrastructure frequently rotated. Check current CISA/FBI advisories for active indicators.

---

## CVEs Exploited

| CVE | Affected Product | Notes |
|---|---|---|
| Various VPN vulnerabilities | Fortinet, Cisco, Citrix, Palo Alto | Initial access via unpatched VPN appliances (specific CVEs vary by campaign/affiliate) |
| Log4Shell (CVE-2021-44228) | Apache Log4j | Used by some affiliates for lateral propagation |
| Exchange ProxyShell variants | Microsoft Exchange | Reported in some affiliate campaigns |

> Note: As a RaaS, specific CVEs exploited vary significantly by affiliate. CISA AA23-353A recommends prioritizing patching of all known exploited vulnerabilities.

---

## YARA / Behavioral Signatures

### Key Behavioral Indicators
- Rust-compiled binary (unusual for ransomware; detectable via PE header analysis)
- Intermittent encryption mode (partial file encryption for speed — file appears corrupted, not fully encrypted)
- Cross-platform targeting: single binary variant for Windows/Linux/VMware
- `vssadmin delete shadows /all /quiet` — shadow copy deletion
- GPO modification for ransomware distribution across domain
- Clearing of Windows Event Logs post-compromise
- Creation of `aadmin` domain account
- Outbound connections to Mega.nz or Dropbox from non-browser processes

---

## Mitigations

1. **Patch VPN/remote access appliances** — primary initial access vector
2. **Enable MFA everywhere** (note: Evilginx2 can bypass SMS MFA; use hardware tokens where possible)
3. **Monitor for `aadmin` account creation** in Active Directory
4. **Alert on vssadmin shadow copy deletion**
5. **Block or monitor Mega.nz / Dropbox** from non-standard processes
6. **Review GPO changes** — unauthorized GPO modifications are a strong indicator
7. **Restrict Task Scheduler** for non-admin users
8. **Deploy EDR capable of detecting POORTRY/STONESTOP** kernel driver abuse
9. **Monitor for Ngrok/Plink** outbound connections
10. **Offline, air-gapped backups** — critical for recovery

---

*IOCs last updated: 2026-04-17 | Author: C3PO*  
*Sources: FBI FLASH CU-000167-MW, CISA AA23-353A (Feb 2024 update), HHS HC3 advisory*
