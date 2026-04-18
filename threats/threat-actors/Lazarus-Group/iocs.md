# 🔍 Lazarus Group — Indicators of Compromise (IOCs)

**Last Updated:** 2026-04-01
**TLP:** TLP:WHITE
**Source Confidence:** A1 (MITRE ATT&CK, CISA, Mandiant, Orange Cyberdefense, Picus Security)

---

## ⚠️ Usage Note
IOCs have a shelf life. Infrastructure rotates rapidly. Treat all indicators as time-sensitive. Cross-reference with threat intel feeds before acting on older entries.

---

## Domains (C2 / Phishing Infrastructure)

### Operation Dream Job / Comebacker 2025 (Active)
| Domain | Type | Campaign | Notes |
|---|---|---|---|
| `birancearea[.]com` | C2 | Comebacker 2025 | Active as of Nov 2025 |
| `hiremployee[.]com` | C2/Phishing | Comebacker 2025 | Active as of Nov 2025 |
| `office-theme[.]com` | C2 | Comebacker 2025 | Reported Nov 2025 |

### Operation HaoBao / Legacy C2
| Domain | Type | Notes |
|---|---|---|
| `worker[.]co[.]kr` | C2 | Legacy; likely inactive |
| `palgong-cc[.]co[.]kr` | C2 | Legacy; likely inactive |
| `celasllc[.]com` | C2 | AppleJeus/TraderTraitor update endpoint |

---

## File Hashes

### Comebacker Variant (Aerospace Campaign, Nov 2025)
| Hash | Type | Notes |
|---|---|---|
| `126961b8c9a7a0e78899943f6c2a7ce9` | MD5 | Comebacker variant |
| `c014a2ac8c89abc3799a520da331caf5` | MD5 | Comebacker variant |
| `d90aeea054ae8cfbd6fca2bd1588a852` | MD5 | Comebacker variant |
| `e4541d91fca9df943b6e119dc1c6cd7f` | MD5 | Comebacker variant |
| `f5475608c0126582081e29927424f338` | MD5 | Comebacker variant |
| `1bfcb157677167c4d5498a0821f3d40691f1e137` | SHA1 | Comebacker variant |
| `6c6419ee544e78448d0641f88ebd3ea2279f4f66` | SHA1 | Comebacker variant |
| `701296f6ff0daf3264dd8814c469b2c7f56df1ec` | SHA1 | Comebacker variant |
| `8e88fd82378794a17a4211fbf2ee2506b9636b02` | SHA1 | Comebacker variant |
| `a0e0a94417e9c594c5c68a6c815160c8b6a980ae` | SHA1 | Comebacker variant |
| `046caa2db6cd14509741890e971ddc8c64ef4cc0e369bd5ba039c40c907d1a1f` | SHA256 | Comebacker variant |
| `14213c013d79ea4bc8309f730e26d52ff23c10654197b8d2d10c82bbbcd88382` | SHA256 | Comebacker variant |
| `7e61c884ce5207839e0df7a22f08f0ab7d483bfa1828090aa260a2f14a0c942c` | SHA256 | Comebacker variant |
| `96b973e577458e5b912715171070c0a0171a3e02154eff487a2dcea4da9fb149` | SHA256 | Comebacker variant |
| `a75886b016d84c3eaacaf01a3c61e04953a7a3adf38acf77a4a2e3a8f544f855` | SHA256 | Comebacker variant |
| `ad9c5aca9977d04c73be579199a827049b6dd9840091ffe8e23acc05e1d4a657` | SHA256 | Comebacker variant |
| `b357b3882cf8107b1cb59015c4be3e0b8b4de80fd7b80ce3cd05081cd3f6a8ff` | SHA256 | Comebacker variant |
| `b7d625679fbcc86510119920ffdd6d21005427bf49c015697c69ae1ee27e6bab` | SHA256 | Comebacker variant |
| `c4a5179a42d9ff2774f7f1f937086c88c4bc7c098963b82cc28a2d41c4449f9e` | SHA256 | Comebacker variant |
| `f2b3867aa06fb38d1505b3c2b9e523d83f906995dcdd1bb384a1087b385bfc50` | SHA256 | Comebacker variant |

---

## URLs (Known Malicious)
| URL | Campaign |
|---|---|
| `https://birancearea[.]com/adminv2` | Comebacker 2025 |
| `https://hiremployee[.]com` | Comebacker 2025 |
| `https://celasllc[.]com/checkupdate.php` | AppleJeus/TraderTraitor |

---

## CVEs (Actively Exploited by Lazarus)
| CVE | CVSS | Component | Notes |
|---|---|---|---|
| `CVE-2024-38193` | Critical | Windows AFD.sys (WinSock driver) | Zero-day; SYSTEM privesc; patched Aug 2024 |
| `CVE-2024-21338` | 7.8 High | Windows Kernel | Privilege escalation; patched Feb 2024 |
| `CVE-2018-4878` | 9.8 Critical | Adobe Flash | Legacy; still seen in unpatched targets |
| `CVE-2021-44228` | 10.0 Critical | Log4Shell | Used in financial targeting |

---

## YARA / Behavioral Indicators

### Suspicious File Paths (Dream Job TTPs)
```
%USERPROFILE%\SumatraPDF.exe  (legitimate app, weaponized as lure)
%USERPROFILE%\TightVNC*.exe   (legitimate VNC, deployed post-compromise)
C:\ProgramData\ThumbNail\thumbnail.db  (DLL loader)
```

### Scheduled Task Pattern
```
schtasks creating tasks that execute remote .xsl scripts via wmic.exe (renamed to nvc.exe)
```

### Registry Persistence
```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run → LNK files pointing to malicious DLLs
```

### Network Behavioral Signatures
- HTTP POST to `/checkupdate.php` endpoints
- GIF89a-prefixed XOR-encoded beacons
- AES-encrypted C2 over HTTPS on non-standard ports
- Outbound connections to compromised SharePoint/WordPress instances

---

## Threat Intelligence Feed References
- AlienVault OTX: [Lazarus Group pulse](https://otx.alienvault.com/adversary/lazarus-group)
- MITRE ATT&CK: [G0032](https://attack.mitre.org/groups/G0032/)
- CISA: [AA21-048A (AppleJeus)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-048a)
- Mandiant: [APT38 Report](https://cloud.google.com/blog/topics/threat-intelligence/apt38-details-on-new-north-korean-regime-backed-threat-group)

---

*IOCs compiled 2026-04-01 | C3PO | TLP:WHITE*
