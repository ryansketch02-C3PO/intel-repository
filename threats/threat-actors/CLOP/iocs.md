# Cl0p (TA505) — Indicators of Compromise (IOCs)

> **TLP: WHITE** | Last updated: 2026-04-14 | Admiralty Grade: A2
> Sources: CISA AA23-158A, CYFIRMA, Blackpoint Cyber, CyberPress, GBHackers, Canadian Centre for Cyber Security

---

## Network IOCs

### IP Addresses (Cleo Campaign — Dec 2024 / Jan 2025)
| IP | Context | Source | Date |
|---|---|---|---|
| `185[.]181.230.103` | Scanning Host | CYFIRMA | Jan 2025 |
| `181[.]214.147.164` | Attacker IP in encoded PowerShell | CYFIRMA | Jan 2025 |
| `176[.]123.5.126` | Attacker IP in encoded PowerShell | CYFIRMA | Jan 2025 |
| `5[.]149.249.226` | Attacker IP in encoded PowerShell | CYFIRMA | Jan 2025 |
| `209[.]127.12.38` | Attacker IP in encoded PowerShell | CYFIRMA | Jan 2025 |
| `192[.]119.99.42` | Attacker IP in encoded PowerShell | CYFIRMA | Jan 2025 |
| `176[.]123.10.115` | Scanning Host | CYFIRMA | Jan 2025 |
| `185[.]162.128.133` | C2 Server | CYFIRMA | Jan 2025 |
| `185[.]163.204.137` | Suspected IOC | CYFIRMA | Jan 2025 |
| `45[.]182.189.102` | Cobalt Strike Server | CYFIRMA | Jan 2025 |
| `89[.]248.172.139` | Suspected IOC | CYFIRMA | Jan 2025 |
| `103[.]140.62.43` | Suspected IOC | CYFIRMA | Jan 2025 |
| `146[.]190.133.67` | Suspected IOC | CYFIRMA | Jan 2025 |
| `162[.]240.110.250` | Suspected IOC | CYFIRMA | Jan 2025 |
| `213[.]136.77.58` | Suspected IOC | CYFIRMA | Jan 2025 |

### IP Addresses (Oracle EBS / Infrastructure Continuity — 2025)
| IP | Context | Source | Date |
|---|---|---|---|
| `185[.]181.60.11` | Oracle EBS exploitation infrastructure (ASN AS56655 Gigahost) | GBHackers / Oracle | Oct 2025 |
| `200[.]107.207.26` | Oracle EBS exploitation infrastructure (ASN AS273045 DATAHOME S.A) | GBHackers / Oracle | Oct 2025 |

> ⚠️ **Infrastructure Note:** Cl0p reuses subnet ranges across campaigns. 41 of 96 IPs in the Oracle campaign subnet match 2023 MOVEit campaign IPs. Subnet `5.188.86/24` has appeared 14 times across multiple exploitation incidents. SSL cert fingerprint `bd613b3be57f18c3bceb0aaf86a28ad8b6df7f9bccacf58044f1068d1787f8a5` links Oracle EBS attacks to MOVEit operations.

---

## Web Shells

| Name | Target Platform | Key Indicators |
|---|---|---|
| **LEMURLOOT** | Progress MOVEit Transfer | Hardcoded password authentication; steals data, manages users; `.aspx` extension |
| **DEWMODE** | Accellion FTA | PHP-based; data exfiltration focus |
| **FileUtils.java** | Oracle E-Business Suite | Java-based downloader/backdoor |

---

## File Indicators

### Ransomware File Extension Patterns
| Extension | Variant / Campaign |
|---|---|
| `.clop` | Cl0p standard (2019–2022) |
| `.Cllp` | Cl0p variant |
| `.Cl0p` | Cl0p variant |
| *(no extension change)* | Encryption-less campaigns (2023–present) — data theft only |

### Malware SHA256 Hashes
> ⚠️ Cl0p frequently compiles custom payloads per campaign. Hash-based detection is insufficient.
> Ingest live feed: **ThreatFox** → https://threatfox.abuse.ch/browse/malware/win.clop/
> Ingest CISA IOCs: https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-158a

---

## Key CVEs (Weaponized by Cl0p)

| CVE | CVSS | Product | Status |
|---|---|---|---|
| CVE-2021-27101 | 9.8 | Accellion FTA | Patched (legacy product, EOL) |
| CVE-2021-27102 | 7.8 | Accellion FTA | Patched (EOL) |
| CVE-2021-27103 | 9.8 | Accellion FTA | Patched (EOL) |
| CVE-2021-27104 | 9.8 | Accellion FTA | Patched (EOL) |
| CVE-2022-31199 | 9.8 | Netwrix Auditor | Patched |
| CVE-2023-0669 | 7.2 | Fortra GoAnywhere MFT | Patched |
| CVE-2023-34362 | 9.8 | Progress MOVEit Transfer | Patched |
| CVE-2023-35036 | 9.1 | Progress MOVEit Transfer | Patched |
| CVE-2023-35708 | 9.8 | Progress MOVEit Transfer | Patched |
| CVE-2024-5806 | 9.1 | Progress MOVEit Transfer SFTP | Patched |
| CVE-2024-50623 | 9.8 | Cleo LexiCom/VLTrader/Harmony | Patched |
| CVE-2024-55956 | Critical | Cleo Harmony/VLTrader/LexiCom | Patched |
| CVE-2025-61882 | Critical | Oracle E-Business Suite | Oracle patches available |

---

## Behavioral / SIEM Detection Signatures

### High-Fidelity Indicators
- `vssadmin.exe delete shadows /all /quiet` — shadow copy deletion pre-encryption
- `taskkill.exe /IM` against AV/EDR process names — security tool termination
- Mass file renaming to `.clop` / `.Cllp` extension
- Web shell activity on MFT servers (especially MOVEit, Cleo): anomalous `.aspx` files in web directories
- Outbound large data transfer to `mega.nz` or `mega.co.nz`
- Encoded/obfuscated PowerShell making outbound connections to new external IPs

### Network Detection
- Unusual outbound connections from MFT servers (MOVEit, GoAnywhere, Cleo)
- Traffic to Mega.nz cloud storage from enterprise servers
- SQL injection patterns against MOVEit web application (POST to `/guestaccess.aspx`, `/api/v1/token`)
- Connections to known C2 IPs listed above

---

## Ransom Note / Communication Indicators

- **Leak site (historical):** `CL0P^_- LEAKS` — Tor-based DLS
- **Communication method:** Victims contacted via email; MOVEit campaign required victims to make first contact
- **Data publication tactic:** Previews stolen data on DLS before full release; uses torrents for leak distribution to complicate takedowns

---

## Threat Hunting Queries (Sigma-style)

```
# Hunt: vssadmin shadow deletion (pre-ransomware indicator)
CommandLine: "vssadmin.exe delete shadows"

# Hunt: Suspicious MFT web shell (LEMURLOOT)
FilePath: "*\MOVEitTransfer\wwwroot\*.aspx" AND EventType: "FileCreate"

# Hunt: Large outbound transfer to Mega
NetworkDestination: "*.mega.nz" OR "*.mega.co.nz" AND BytesSent > 100MB

# Hunt: PowerShell with encoded command + new external connection
CommandLine: "powershell*-EncodedCommand*" AND NetworkConnection: true
```

---

## Intelligence Feed Links

| Feed | URL |
|---|---|
| ThreatFox (Cl0p) | https://threatfox.abuse.ch/browse/malware/win.clop/ |
| CISA Advisory AA23-158A | https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-158a |
| CISA TrueBot Advisory | https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-200a |
| CYFIRMA Cleo Campaign | https://www.cyfirma.com/research/cl0p-ransomware-latest-attacks/ |
