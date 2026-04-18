# APT40 (TEMP.Periscope / Leviathan / GINGHAM TYPHOON)

## Identity

| Field | Value |
|---|---|
| **Scoundrel #** | 017 |
| **Primary Name** | APT40 |
| **AKA** | TEMP.Periscope, TEMP.Jumper, Leviathan, BRONZE MOHAWK, Kryptonite Panda, GINGHAM TYPHOON, MUDCARP, Gadolinium, GreenCrash, Hellsing, FEVERDREAM, Red Ladon, TA423, G0065 |
| **Type** | State-Sponsored Cyber Espionage |
| **Origin** | People's Republic of China (PRC) |
| **Sponsor** | Ministry of State Security (MSS) — Hainan State Security Department (HSSD) |
| **Front Company** | Hainan Xiandun Technology Development Co. Ltd. |
| **Base** | Haikou, Hainan Province, PRC |
| **Active Since** | ~2009 |
| **Status** | 🔴 ACTIVE |
| **Threat Level** | 🔴 HIGH |
| **MITRE ATT&CK ID** | G0065 |

---

## Mission

APT40 conducts long-term cyber espionage on behalf of the PRC MSS, stealing intellectual property, trade secrets, and sensitive government data. Primary focus areas align with China's **Belt and Road Initiative**, **Made in China 2025** industrial strategy, and PLA military modernization. The group prioritizes **maritime, aerospace, and defense** sectors — intel that directly feeds Beijing's strategic military and economic ambitions.

---

## Known Indicted Members

| Name | Role |
|---|---|
| Ding Xiaoyang | MSS HSSD Intelligence Officer — directed operations |
| Zhu Yunmin | MSS HSSD Intelligence Officer — directed operations |
| Cheng Qingmin | MSS HSSD Intelligence Officer — directed operations |
| Wu Shurong | Hainan Xiandun employee — conducted CNE activity |

*Indicted by U.S. DOJ on July 19, 2021. Remain at large in PRC.*

---

## Targeting

### Primary Sectors
- ✈️ **Aerospace & Aviation** — top priority, historical theft of aircraft/spacecraft tech
- 🛡️ **Defense Industrial Base** — contractor networks, procurement data
- 🚢 **Maritime / Shipping** — ships, ports, ocean research (South China Sea interest)
- 🏛️ **Government & Military** — U.S., Australia, Europe, ASEAN
- 🔬 **Research Universities** — technology and science programs
- 🏥 Biomedical / Healthcare
- 🏭 Manufacturing & Transportation (Rail/Shipping)

### Geographic Focus
- United States, Australia, United Kingdom, Canada
- Western Europe (Germany, France)
- South China Sea region (Philippines, Vietnam, Malaysia)
- Middle East & Belt and Road Initiative countries

---

## TTPs (MITRE ATT&CK)

### Preferred Initial Access
APT40 **strongly prefers exploiting public-facing applications** over phishing — faster, stealthier, less user interaction required.

| Technique | ATT&CK ID | Notes |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Log4Shell, Atlassian Confluence, MS Exchange ProxyShell |
| External Remote Services (VPN) | T1133 | Exploit remote access gateways |
| Spearphishing Attachment | T1566.001 | Used when infra exploitation isn't available |
| Spearphishing Link | T1566.002 | Lure documents with embedded malware |
| Drive-by Compromise | T1189 | Watering hole attacks |
| Valid Accounts (Compromised Admin) | T1078.001 | Credential theft and reuse |

### Persistence & Lateral Movement
| Technique | ATT&CK ID |
|---|---|
| Web Shell deployment | T1505.003 |
| Internal Spearphishing | T1534 |
| SMB/Windows Admin Shares | T1021.002 |
| Remote Services | T1021 |

### Defense Evasion & C2
| Technique | ATT&CK ID | Notes |
|---|---|---|
| Compromised SOHO devices as C2 | T1584.008 | **Signature recent tactic** — blends with legit traffic |
| Protocol Impersonation | T1001.003 | Traffic designed to look legitimate |
| Typosquatting C2 domains | T1583.001 | Impersonate real organizations |
| Steganography | T1027.003 | Hidden data in files on GitHub |
| Dropbox API for exfil | T1567.002 | Disguise exfil as cloud sync |
| Tor / Multi-hop Proxies | T1090.003 | Operational anonymization |
| Domain Typosquatting | T1583.001 | airbusocean[.]com, teledynegroup[.]com, etc. |

### Collection & Exfil
| Technique | ATT&CK ID |
|---|---|
| Network Sniffing (tcpdump) | T1040 |
| Web Session Cookie Theft (JWT capture) | T1539 |
| Input Capture: Web Portal Capture | T1056.003 |
| Archive & Encrypt Staged Data | T1560 / T1532 |
| Exfiltration over C2 Channel | T1041 |

---

## Malware & Tools Arsenal

### Custom Malware
| Tool | Notes |
|---|---|
| BADFLICK / GreenCrash | Custom backdoor |
| AIRBREAK / Orz | JavaScript recon/exploit framework |
| DADBOD | Custom tool, not shared with other groups |
| DADJOKE | Custom tool, not shared with other groups |
| NanHaiShu (S0228) | JS-based RAT targeting South China Sea entities |
| jjdoor / Transporter | Backdoor |
| jumpkick | Post-exploitation tool |
| MurkTop (mt.exe) | Reconnaissance tool |
| GreenRAT | Lightweight backdoor |

### Open-Source / Shared Tools
| Tool | Notes |
|---|---|
| China Chopper (S0020) | Web shell — widely used by PRC actors |
| Cobalt Strike (S0154) | Beacon for C2 |
| Derusbi / PHOTO (S0021) | Shared across multiple PRC APT groups |
| Gh0stRAT (S0032) | Legacy PRC RAT, still in use |
| PowerShell Empire (S0363) | Post-exploitation framework |
| PowerSploit (S0194) | PowerShell attack suite |
| Mimikatz | Credential dumping |
| Meterpreter | Post-exploitation (Metasploit) |
| ScanBox | Web recon framework |

---

## Notable Operations

| Year | Target | Description |
|---|---|---|
| 2011–2018 | US Aerospace/Defense, Universities | Core CNE campaign via Hainan Xiandun front co. — theft of trade secrets, IP |
| 2017–2018 | US/EU Maritime & Engineering firms | TEMP.Periscope campaign — shipping, shipbuilding, engineering espionage |
| 2018 | Southeast Asian Defense / Energy | Regional targeting aligned with South China Sea disputes |
| 2021 | Microsoft Exchange servers globally | Rapid exploitation of ProxyShell (CVE-2021-31207, CVE-2021-34523/34473) |
| 2021 | Atlassian Confluence | Exploitation of CVE-2021-26084 / CVE-2021-31207 |
| 2021–2022 | Log4j (CVE-2021-44228) | Fast-follow exploitation within hours of PoC release |
| 2022 (Apr) | Australian organization (remote access) | Stole hundreds of credentials via web shell on login portal |
| 2022 (Jul–Sep) | Australian organization (network) | Full network compromise, lateral movement, data exfil |
| 2024 | Australian government/private sector | Ongoing active targeting per Five Eyes advisory (July 2024) |

---

## Key CVEs Exploited

| CVE | Product | Year |
|---|---|---|
| CVE-2021-44228 | Apache Log4j (Log4Shell) | 2021 |
| CVE-2021-26084 | Atlassian Confluence RCE | 2021 |
| CVE-2021-31207 | Microsoft Exchange (ProxyShell) | 2021 |
| CVE-2021-34523 | Microsoft Exchange (SSRF) | 2021 |
| CVE-2021-34473 | Microsoft Exchange (RCE) | 2021 |

---

## Threat Intelligence Grading

| Field | Value |
|---|---|
| **Source Reliability** | A (CISA/FBI/Five Eyes joint advisory; DOJ indictment) |
| **Info Credibility** | 1 (Confirmed by multiple independent government agencies) |
| **Admiralty Grade** | **A1** |
| **TLP** | TLP:CLEAR |

---

## Attribution

- **U.S. DOJ Indictment:** July 19, 2021 — four named actors
- **FBI/CISA Joint Advisory:** AA21-200A (July 2021)
- **Five Eyes + Germany, Japan, South Korea Joint Advisory:** July 9, 2024 (led by ASD's ACSC)
- **Links:** Confirmed MSS tasking via Hainan HSSD; front company Hainan Xiandun

---

## Connection Web

- **Volt Typhoon (#008):** Both PRC MSS/PLA-linked; Volt Typhoon focuses critical infrastructure while APT40 emphasizes IP theft — parallel tracks of PRC cyber strategy
- **Salt Typhoon (#010):** Both MSS-linked; different sectors (Salt Typhoon = telecom) but same intelligence apparatus
- **APT28/Fancy Bear (#006):** Structural parallel — GRU-backed espionage actor for Russia vs. MSS-backed for PRC; similar spear-phishing and exploitation playbook

---

## Defensive Recommendations

1. **Patch within 48 hours** — APT40 weaponizes public PoCs within hours of release. Speed is everything.
2. **Audit internet-facing infrastructure** — VPNs, remote access gateways, web apps are primary targets.
3. **Log everything** — web server request logs, Windows event logs, proxy logs. APT40 exploits logging gaps.
4. **MFA on all remote access** — VPN, RDP, webmail, cloud platforms.
5. **Network segmentation** — limit lateral movement; protect AD servers behind jump servers.
6. **Replace end-of-life SOHO devices** — APT40 pivots through compromised routers as C2.
7. **Hunt for web shells** — especially after exploitation of web apps.
8. **Monitor for typosquatted domains** in DNS logs (see IOCs).

---

## References

- FBI/CISA Advisory AA21-200A: https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-200a
- DOJ Indictment (July 2021): https://www.justice.gov/opa/pr/four-chinese-nationals-working-ministry-state-security-charged-global-computer-intrusion
- ASD's ACSC / Five Eyes Advisory (July 2024): https://www.cyber.gov.au/about-us/view-all-content/alerts-and-advisories/apt40-advisory-prc-mss-tradecraft-in-action
- MITRE ATT&CK: https://attack.mitre.org/groups/G0065/

---

*Profile created: 2026-04-13 | Next review: 2026-07-13 | Classification: TLP:CLEAR*
