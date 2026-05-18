# SHADOW-EARTH-053 — Indicators of Compromise (IOCs)

> **Last Updated:** 2026-05-18
> **Admiralty Grade:** B2 | TLP:CLEAR
> ⚠️ **Single-vendor (Trend Micro) disclosure. IOCs should be treated as moderate-confidence pending corroboration from additional vendors. ShadowPad and NoodleRat detections are high-value given their uniqueness to China-aligned actors.**

---

## Malware Detections

| Tool | Detection Guidance | Notes |
|---|---|---|
| **ShadowPad** | EDR: Hunt for ShadowPad DLL signatures; multiple AV vendor sigs exist | Deployed after 8-month dwell period — already-compromised systems |
| **NoodleRat** (Linux) | Linux EDR / file integrity monitoring | Deployed via React2Shell (CVE-2025-55182) exploitation |
| **Godzilla Web Shell** | Web server file integrity scan; YARA rules widely available | JSP/PHP web shell; deployed on Exchange servers |

---

## Exploit Indicators

| CVE | Product | Notes |
|---|---|---|
| CVE-2021-26855 | Microsoft Exchange | ProxyLogon; primary initial access |
| CVE-2021-26857 | Microsoft Exchange | ProxyLogon chain |
| CVE-2021-26858 | Microsoft Exchange | ProxyLogon chain |
| CVE-2021-27065 | Microsoft Exchange | ProxyLogon chain |
| CVE-2025-55182 | React Server Components | React2Shell; used in Linux NoodleRat deployment |

---

## Behavioral Indicators

| Indicator | Stage | Notes |
|---|---|---|
| Exchange `w3wp.exe` spawning cmd.exe or PowerShell | Initial Access | ProxyLogon post-exploitation |
| Godzilla web shell files in Exchange directories | Persistence | `.jsp` / `.aspx` files in OWA directories |
| `WMIC /node` lateral movement | Lateral Movement | Used to install backdoors on additional hosts |
| ShadowPad DLL in `C:\Windows\System32` or `C:\ProgramData` | Persistence | Long-dwell deployment; check timestamps |
| React application spawning unexpected child processes | Initial Access | React2Shell exploitation indicator |

---

## Network Indicators

- C2 infrastructure not publicly disclosed by Trend Micro as of 2026-05-18
- Monitor for outbound C2 traffic from ShadowPad (encrypted, non-standard ports common)

---

## Hunting Notes

- **Long dwell detection**: SHADOW-EARTH-053 maintained access for up to 8 months before deploying ShadowPad. Hunt for initial-access-phase artifacts (web shells, scheduled tasks) that predate current-quarter indicators.
- **ShadowPad is the high-confidence IOC**: Any confirmed ShadowPad detection should be treated as a nation-state intrusion regardless of specific group attribution.

---

*IOCs compiled: 2026-05-18 | Source: Trend Micro / The Register | Admiralty Grade: B2 | TLP:CLEAR*
