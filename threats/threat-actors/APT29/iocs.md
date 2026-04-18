# 🔍 APT29 — Indicators of Compromise

> **Last Updated:** 2026-04-06
> **Admiralty Grade:** A1 (confirmed nation-state, Five Eyes + EU attribution)
> **TLP:** TLP:WHITE
>
> ⚠️ APT29 rotates infrastructure extremely aggressively — especially post-exposure.
> Behavioral/TTP detection is far more durable than static IOC matching.
> Treat all domains and IPs as time-bounded. Cross-reference CISA AA24-057A for latest.

---

## CVEs Actively Exploited

| CVE | CVSS | Product | Description | Status |
|---|---|---|---|---|
| CVE-2023-42793 | 9.8 CRITICAL | JetBrains TeamCity | Authentication bypass; used for supply chain access | CISA KEV — Patch Now |
| CVE-2021-1879 | 6.1 | Apple iOS/iPadOS/watchOS | WebKit XSS; used in watering hole campaigns | Patch Now |
| CVE-2020-4006 | 7.2 | VMware Products | Command injection; ADFS/identity attacks | Patch Now |
| CVE-2020-0688 | 8.8 | Microsoft Exchange | ECP deserialization RCE; used during SolarWinds phase | Legacy — Patch |
| CVE-2019-19781 | 9.8 CRITICAL | Citrix ADC/Gateway | Code execution via directory traversal | Patch Now |
| CVE-2019-11510 | 10.0 CRITICAL | Ivanti Pulse Connect Secure | Arbitrary file read; credential theft | Patch Now — Product EOL |
| CVE-2019-9670 | 9.8 CRITICAL | Synacor Zimbra | XXE → SSRF → RCE | Patch Now |
| CVE-2018-13379 | 9.8 CRITICAL | Fortinet FortiGate VPN | Path traversal → credential theft | Patch Now |

---

## Domains — Midnight Blizzard AWS-Spoofing Campaign (Aug–Oct 2024)

These domains were used to spoof AWS and other legitimate organizations in credential-phishing campaigns targeting Ukraine-linked government, military, and enterprise entities.

| Domain | Context |
|---|---|
| `aws-data[.]cloud` | AWS spoof — credential harvest |
| `aws-il[.]cloud` | AWS spoof — credential harvest |
| `aws-join[.]cloud` | AWS spoof — credential harvest |
| `aws-meet[.]cloud` | AWS spoof — credential harvest |
| `aws-meetings[.]cloud` | AWS spoof — credential harvest |
| `aws-online[.]cloud` | AWS spoof — credential harvest |
| `awsplatform[.]online` | AWS spoof — credential harvest |
| `aws-s3[.]cloud` | AWS spoof — credential harvest |
| `aws-secure[.]cloud` | AWS spoof — credential harvest |
| `aws-ukraine[.]cloud` | AWS spoof — Ukraine-specific lure |
| `amazonmeeting[.]cloud` | AWS/Amazon spoof |
| `amazonsolutions[.]cloud` | AWS/Amazon spoof |
| `eru-gov[.]cloud` | EU gov entity spoof |
| `feedzai-gov[.]cloud` | Feedzai fintech spoof |
| `4freerussia[.]cloud` | Russian opposition NGO lure |
| `albrightstonebridge[.]cloud` | Think tank spoof (Albright Stonebridge Group) |
| `americanprogress[.]cloud` | Think tank spoof (Center for American Progress) |
| `aspeninstitute[.]cloud` | Think tank spoof (Aspen Institute) |
| `brookings[.]cloud` | Think tank spoof (Brookings Institution) |
| `bund-de[.]cloud` | German federal gov spoof |
| `admin-ch[.]cloud` | Swiss federal admin spoof |
| `barracuda[.]solutions` | Barracuda security product spoof |
| `backupify[.]cloud` | Cloud backup service spoof |
| `caci[.]solutions` | Defense contractor (CACI) spoof |

---

## IP Addresses — Midnight Blizzard C2 Infrastructure (2024–2025)

| IP Address | Context |
|---|---|
| `185.187.155[.]81` | C2 infrastructure |
| `185.216.72[.]182` | C2 infrastructure |
| `185.216.72[.]185` | C2 infrastructure |
| `185.216.72[.]192` | C2 infrastructure |
| `185.216.72[.]196` | C2 infrastructure (high frequency) |
| `185.76.79[.]178` | C2 infrastructure (high frequency) |
| `23.160.56[.]100` | C2 infrastructure |
| `23.160.56[.]122` | C2 infrastructure (high frequency) |
| `38.180.110[.]238` | C2 infrastructure (high frequency) |
| `38.180.146[.]210` | C2 infrastructure (high frequency) |
| `38.180.146[.]230` | C2 infrastructure (high frequency) |
| `45.11.230[.]105` | C2 infrastructure (high frequency) |
| `45.11.231[.]8` | C2 infrastructure (high frequency) |
| `45.134.110[.]83` | C2 infrastructure (high frequency) |
| `45.134.111[.]123` | C2 infrastructure |
| `45.141.58[.]60` | C2 infrastructure (high frequency) |
| `45.67.85[.]40` | C2 infrastructure (high frequency) |
| `45.80.193[.]9` | C2 infrastructure (high frequency) |
| `46.19.141[.]186` | C2 infrastructure (high frequency) |
| `62.72.7[.]213` | C2 infrastructure (high frequency) |
| `84.32.188[.]148` | C2 infrastructure (high frequency) |
| `89.46.234[.]115` | C2 infrastructure (high frequency) |
| `95.217.113[.]133` | C2 infrastructure (high frequency) |
| `193.29.59[.]9` | C2 infrastructure (high frequency) |
| `2.58.201[.]112` | C2 infrastructure (high frequency) |
| `5.133.9[.]252` | C2 infrastructure (high frequency) |

---

## Domains — GRAPELOADER European Diplomat Campaign (2025)

| Domain/URL | Context |
|---|---|
| `ceo-adviser[.]com/fb-connect.php` | GRAPELOADER C2 callback URL |
| `track-adv[.]com/analytics.php` | Watering hole analytics tracking (iOS exploit) |
| `track-adv[.]com/market-analytics.php` | Watering hole delivery (Chrome Android exploit) |

---

## File Hashes — Malware Samples

### GRAPELOADER / Diplomat Campaign (2025)
| SHA-256 | Description |
|---|---|
| `280fbf353fdffefc5a0af40c706377142fff718c7b87bc8b0daab10849f388d0` | GRAPELOADER dropper |
| `8b45f5a173e8e18b0d5c544f9221d7a1759847c28e62a25210ad8265f07e96d5` | GRAPELOADER payload |

### Watering Hole Exploit Payloads (Mongolia gov / iOS+Chrome campaign)
| SHA-256 | Description |
|---|---|
| `21682218bde550b2f06ee2bb4f6a39cff29672ebe27acbb3cee5db79bf6d7297` | iOS WebKit exploit payload |
| `8bd9a73da704b4d7314164bff71ca76c15742dcc343304def49b1e4543478d1a` | Chrome Android exploit payload |

### Midnight Blizzard General (multiple campaigns — SHA-1)
| SHA-1 | Description |
|---|---|
| `395da6d4f3c890295f7584132ea73d759bd9d094` | Malware artifact |
| `72e5fc82b932c5395d06fd2a655a280cf10ac9aa` | Malware artifact |
| `75af292f34789a1c782ea36c7127bf6106f595e8` | Malware artifact |
| `76640508b1e7759e548771a5359eaed353bf1eec` | Malware artifact |
| `9858d5cb2a6614be3c48e33911bf9f7978b441bf` | Malware artifact |

---

## Malware Behavioral Indicators

### SUNBURST (SolarWinds 2020)
- DLL name: `SolarWinds.Orion.Core.BusinessLayer.dll` (trojanized)
- C2 pattern: DNS queries to `*.avsvmcloud[.]com` (avsvmcloud.com was the C2 domain)
- Traffic masqueraded as Orion Improvement Program (OIP) protocol
- 12–14 day dormancy period before first C2 contact post-install

### FOGGYWEB (ADFS Backdoor)
- Installed on ADFS servers as a passive backdoor HTTP listener
- Exfiltrates SAML signing certificates and token issuance requests
- Survives server reboots; low-footprint HTTP module loaded by ADFS process

### QUIETEXIT (Opaque Device Implant)
- Reverse SSH tunnel installed on SAN arrays, load balancers, WAP controllers
- Configured to forward connections to internal RDP (3389), SMB (445), NetBIOS (139)
- No persistence mechanism by default; APT29 adds startup scripts manually

### VoltDrop (Schneider Electric SCADA — 2026)
- Operates at Modicon PLC firmware level
- Survives firmware reflash via hidden bootloader modification
- C2 communication disguised as legitimate SCADA Modbus polling on TCP/502
- Lateral movement capability to connected IT networks via engineering workstations

---

## YARA Signatures (Reference)

```yara
rule APT29_SUNBURST_SolarWinds {
    meta:
        description = "Detects SUNBURST trojanized SolarWinds DLL"
        author = "C3PO Intel"
        reference = "SolarWinds 2020 supply chain compromise"
        tlp = "WHITE"
    strings:
        $s1 = "SolarWinds.Orion.Core.BusinessLayer" ascii wide
        $s2 = "avsvmcloud" ascii
        $s3 = "OrionImprovementBusinessLayer" ascii
    condition:
        all of them
}

rule APT29_FOGGYWEB {
    meta:
        description = "Detects FOGGYWEB ADFS backdoor behavioral indicators"
        author = "C3PO Intel"
        reference = "Microsoft MSTIC FOGGYWEB report"
        tlp = "WHITE"
    strings:
        $s1 = "Microsoft.IdentityServer.Web" ascii wide
        $s2 = "SecurityTokenService" ascii wide
        $s3 = { 48 8B 05 ?? ?? ?? ?? 48 85 C0 74 }
    condition:
        2 of them
}
```

---

## Detection Priorities

| Priority | Signal | Why It Matters |
|---|---|---|
| 🔴 CRITICAL | Cloud sign-ins from residential proxy IPs / geographically impossible travel | APT29 primary initial access vector 2024–2026 |
| 🔴 CRITICAL | Unexpected ADFS DLL loads or anomalous token issuance | FOGGYWEB/MAGICWEB indicators |
| 🔴 CRITICAL | ApplicationImpersonation grants in Exchange / EWS API access | Mailbox exfiltration setup |
| 🟠 HIGH | DNS queries to newly registered `.cloud` / `.online` / `.solutions` domains | AWS-spoof phishing infrastructure pattern |
| 🟠 HIGH | Signed RDP files in email attachments | Oct 2024 mass phishing campaign TTP |
| 🟠 HIGH | PowerShell downloading from cloud storage (OneDrive, Dropbox) | Duke malware stage 2 delivery |
| 🟡 MEDIUM | SUNBURST IoC pattern in DNS (avsvmcloud[.]com subdomains) | Historical but worth watching for copycats |

---

## References

- [CISA Advisory AA24-057A — SVR Cloud Tactics (Feb 2024)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-057a)
- [Microsoft MSTIC — Midnight Blizzard RDP Campaign (Oct 2024)](https://www.microsoft.com/en-us/security/blog/2024/10/29/midnight-blizzard-conducts-large-scale-spear-phishing-campaign-using-rdp-files/)
- [Check Point Research — GRAPELOADER (2025)](https://research.checkpoint.com/2025/apt29-phishing-campaign/)
- [IOC.one — Midnight Blizzard IOC Series (Parts 15–18)](https://ioc.one/)
- [DarkRelay — APT29 IOCs & Emulation Plan (2026)](https://www.darkrelay.com/post/apt29-midnight-blizzard-operations-iocs-detection-strategies)

---

*Last Updated: 2026-04-06 | Maintained by C3PO Intel*
