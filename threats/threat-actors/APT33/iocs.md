# APT33 — IOCs

> Last updated: 2026-04-19
> Sources: Microsoft MSTIC, Mandiant, Symantec, BrandDefense, MITRE ATT&CK

---

## Network Indicators

### Domains (Historical / Phishing Infrastructure)
| Domain | Notes |
|---|---|
| `boeing.servehttp[.]com` | Domain masquerading Boeing — spearphishing lure |
| `azurewebsites[.]net` subdomains | Tickler C2 — attacker-controlled Azure subscriptions (pattern, not specific subdomain) |

### User Agent
| Indicator | Type | Notes |
|---|---|---|
| `go-http-client` | HTTP User-Agent | Password spray campaigns — high-confidence APT33 indicator in Azure AD / Entra ID auth logs |

### Infrastructure Pattern
| Pattern | Notes |
|---|---|
| TOR exit nodes → Azure AD authentication | Password spray origin anonymisation |
| Fraudulent Azure "for Students" subscriptions | C2 infrastructure created via compromised education accounts |
| Non-standard HTTP ports 808, 880 | Legacy TURNEDUP/POWERTON C2 |

---

## Host-Based Indicators

### Registry
| Key | Value | Notes |
|---|---|---|
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | `SharePoint.exe` | Tickler persistence — NOT a legitimate Microsoft file |

### Files / Processes
| Indicator | Type | Notes |
|---|---|---|
| `SharePoint.exe` (in Run key, not SharePoint install dir) | Tickler persistence | Masquerades as Microsoft SharePoint |
| `qemu-system-x86_64.exe` | N/A (not APT33-specific) | See Payouts King — listed here for awareness |
| `.pdf.exe` double-extension files in ZIP archives | Tickler delivery | ZIP masquerade delivery method |
| `vault.db`, `bisrv.dll` | N/A | Payouts King cross-ref only |

### Scheduled Tasks
| Task Name | Notes |
|---|---|
| Malicious tasks running `.vbe` files multiple times per day | Legacy persistence — TURNEDUP era |

### Tools Deployed Post-Compromise
| Tool | Legitimacy | Detection Note |
|---|---|---|
| AzureHound | Open source | Cloud recon; alert on unexpected execution |
| Roadtools | Open source | Azure AD/Entra ID mapping; alert on unexpected execution |
| AnyDesk | Legitimate RMM | Unapproved deployment = persistence indicator |
| AD Explorer (Sysinternals) | Legitimate | Taking AD snapshots = recon indicator |
| Mimikatz | Offensive | LSASS credential dumping |
| LaZagne | Offensive | Multi-platform credential harvesting |
| ProcDump | Legitimate (Sysinternals) | Used for LSASS dump — monitor execution against lsass.exe |
| Rclone | Legitimate | Data exfil staging tool |

---

## Malware Hashes (Known Samples)

> Note: APT33 rotates infrastructure and recompiles frequently. Hashes are historical indicators; use behavioural detections as primary.

| SHA256 | Family | Notes |
|---|---|---|
| (Tickler samples — see Microsoft MSTIC Aug 2024 report for full hash list) | Tickler | Multi-stage backdoor |
| (FalseFont samples — see Microsoft MSTIC Dec 2023 report for full hash list) | FalseFont | DIB-targeted backdoor |

---

## CVEs Exploited

| CVE | CVSS | Product | Status | Notes |
|---|---|---|---|---|
| CVE-2017-11774 | 7.8 HIGH | Microsoft Outlook | On CISA KEV — patched | Outlook Home Page abuse; used with Ruler tool |
| CVE-2018-20250 | 7.8 HIGH | WinRAR | On CISA KEV — patched | ACE path traversal; .rar archive delivery |

---

## MITRE ATT&CK Techniques (Detection Reference)

| ID | Name | Priority |
|---|---|---|
| T1110.003 | Password Spraying | 🔴 Critical — primary initial access |
| T1550.003 | Web Session Cookie (Golden SAML) | 🔴 Critical — MFA bypass |
| T1078.004 | Valid Accounts: Cloud | 🔴 Critical |
| T1059.001 | PowerShell | 🟡 High |
| T1021.002 | SMB/Windows Admin Shares | 🟡 High |
| T1485 | Data Destruction (SHAPESHIFT) | 🔴 Critical — if deployed |
| T1547.001 | Registry Run Keys | 🟡 High |
| T1087 | Account Discovery | 🟡 Medium |

---

## Detection Rules (Recommended)

```
# Azure AD / Entra ID — Password Spray Detection
UserAgent = "go-http-client" AND AuthResult = "Failure"
→ Alert if >20 distinct accounts, single IP, within 60 minutes

# Tickler Registry Persistence
RegistryValueName = "SharePoint.exe"
AND RegistryKey CONTAINS "CurrentVersion\Run"
AND FilePath NOT CONTAINS "SharePoint"
→ Alert immediately

# Golden SAML — AD FS Token Abuse
EventID = 1007 (AD FS) AND TokenIssuer = unexpected source
→ Alert immediately
```

---

*IOC file created: 2026-04-19 | Cross-reference with Microsoft MSTIC Tickler report (Aug 2024) and FalseFont report (Dec 2023) for current hash lists.*
