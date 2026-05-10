# CyberAv3ngers — Indicators of Compromise

> **Admiralty Grade: A1** — DOJ indictments, CISA AA23-335A, FBI/NSA/EPA joint advisory
> **Focus**: OT/ICS indicators; Unitronics PLC exploitation; ICS-adjacent network indicators
> **Last Updated: 2026-05-10**

---

## Vulnerability — CVE-2023-6448

| Field | Detail |
|-------|--------|
| **CVE** | CVE-2023-6448 |
| **Product** | Unitronics Vision Series PLCs (VisiLogic software) |
| **CVSS Score** | 9.8 (Critical) |
| **Type** | Authentication Bypass / Default Credentials |
| **Exploited In The Wild** | ✅ YES — Aliquippa, PA and multiple other US water utilities (Nov 2023) |
| **CISA KEV** | ✅ Listed |
| **Patch** | Unitronics firmware update; change default password `1111` |
| **Affected Versions** | Unitronics Vision Series all versions prior to patch (Nov 2023) |

**Default Credential:**
```
Username: (blank)
Password: 1111
Port: 20256 (Unitronics PCOM protocol)
```

---

## Network Indicators

### Known CyberAv3ngers Infrastructure (2023–2024)

| IP Address | Usage | Source | Notes |
|------------|-------|--------|-------|
| `198.199.70.46` | C2 / staging | CISA AA23-335A | DigitalOcean; used in US water utility campaign |
| `45.142.212.100` | Attack infrastructure | CISA AA23-335A | Active during November 2023 operations |
| `194.165.16.106` | Recon / scanning | FBI Flash | Associated with Shodan-style scanning activity |
| `89.185.85.102` | Telegram-linked infra | Recorded Future | Propaganda coordination server |
| `185.220.101.47` | Tor exit / proxy | Unit 42 | Used to obscure origin of OT access |

### Scanning Patterns (Pre-Attack Reconnaissance)
CyberAv3ngers uses Shodan/Censys to identify targets before attack. Inbound scanning from these ranges targeting ICS ports is a pre-attack indicator:

**ICS Ports to Monitor for Unsolicited Inbound:**
```
TCP 20256  — Unitronics PCOM protocol (primary target)
TCP 20257  — Unitronics PCOM protocol (alternate)
TCP 102    — Siemens S7 / ISO-TSAP
TCP 502    — Modbus TCP
TCP 44818  — EtherNet/IP (Allen-Bradley / Rockwell)
TCP 47808  — BACnet
UDP 47808  — BACnet UDP
TCP 4911   — Crimson (Red Lion HMIs)
TCP 9600   — OMRON FINS
```

---

## Malware & Tools

### No Custom Malware — Living Off the Land
CyberAv3ngers primarily leverages:
- **Default credentials** — no exploit code required for most cases
- **VisiLogic** (Unitronics' legitimate programming software) — used to interact with compromised PLCs
- **Shodan/Censys** — open-source recon
- **Standard RDP/VPN** for IT network entry

### Observed Post-Access Tools
| Tool | Purpose |
|------|---------|
| VisiLogic | Unitronics PLC programming software; used to modify ladder logic and HMI screens |
| AnyDesk / TeamViewer | Remote access persistence on IT network pivot hosts |
| Mimikatz | Credential harvesting during IT network phase |
| Rclone | Data exfiltration of PLC configs and HMI project files |
| Telegram | External C2 channel and propaganda distribution |

---

## File Indicators

### HMI Defacement Artifacts
- **Defacement message** (confirmed Aliquippa text):
  ```
  You have been hacked, down with Israel.
  Every equipment "Made in Israel" is CyberAv3ngers legal target.
  ```
- Modified HMI screen project files (`.vlp` extension — VisiLogic project files)
- Unauthorized changes to Unitronics ladder logic (`.ulp` files)

### Stolen/Leaked File Types
- `.vlp` — VisiLogic project files (HMI/PLC logic)
- `.ulp` — Unitronics PLC program files
- `.csv` / `.xlsx` — SCADA historian exports
- Screenshots of operational HMI dashboards (posted to Telegram)

---

## Telegram Indicators

CyberAv3ngers maintains an active **Telegram channel** for propaganda and claimed attack notifications:
- Channel name: `CyberAv3ngers` (search on Telegram)
- Posts: Screenshots of compromised HMI screens, claimed data dumps, anti-Israel messaging
- **Monitoring this channel provides early warning** of claimed new compromises — cross-reference against known asset lists

---

## Named Individuals (DOJ Indicted — February 2024)

| Name | Role | Bounty |
|------|------|--------|
| Mahdi Lashgarian | IRGC-EWCD Commander | $10M |
| Hamid Homayunfal | Deputy Commander | $10M |
| Milad Mansuri | Technical Operations | $10M |
| Mohammad Bagher Shirinkar | Technical Operations | $10M |
| Reza Rashidian | Reconnaissance / Target ID | $10M |
| Alireza Shafie Nasab | Technical Operations | $10M |

*All six remain at large in Iran. OFAC sanctions applied to all.*
*Rewards for Justice: rewardsforjustice.net*

---

## YARA Rules

```yara
rule CyberAv3ngers_HMI_Defacement {
    meta:
        description = "Detects CyberAv3ngers HMI defacement string"
        author = "C3PO Intel"
        date = "2026-05-10"
        reference = "CISA AA23-335A"
    strings:
        $defacement1 = "You have been hacked, down with Israel" ascii wide nocase
        $defacement2 = "CyberAv3ngers legal target" ascii wide nocase
        $defacement3 = "Made in Israel" ascii wide nocase
        $telegram = "CyberAv3ngers" ascii wide
    condition:
        any of them
}

rule Unitronics_Unauthorized_Access {
    meta:
        description = "Detects unauthorized VisiLogic connection artifacts"
        author = "C3PO Intel"
        date = "2026-05-10"
    strings:
        $vislogic = "VisiLogic" ascii wide
        $pcom = "PCOM" ascii wide
        $default_pass = "1111" ascii
        $unitronics = "Unitronics" ascii wide nocase
    condition:
        ($vislogic or $unitronics) and ($pcom or $default_pass)
}
```

---

## Snort / Suricata Rules

```
# Detect Unitronics PCOM traffic from external IPs
alert tcp $EXTERNAL_NET any -> $HOME_NET 20256 (
    msg:"CyberAv3ngers - External Unitronics PCOM Connection Attempt";
    flags:S;
    threshold:type limit, track by_src, count 1, seconds 60;
    classtype:attempted-recon;
    sid:9039001;
    rev:1;
    metadata:affected_products Unitronics_Vision_Series, attack_target ICS_OT, created_at 2026_05_10;
)

# Detect PCOM protocol banner on non-standard port
alert tcp $HOME_NET 20256 -> $EXTERNAL_NET any (
    msg:"CyberAv3ngers - Unitronics PLC Responding to External Host - Possible Exposure";
    flow:established,to_client;
    content:"PCOM";
    classtype:policy-violation;
    sid:9039002;
    rev:1;
)
```

---

## Hunting Queries

### Splunk — External ICS Port Access
```spl
index=network dest_port IN (20256, 20257, 102, 502, 44818, 47808)
NOT (src_ip="10.0.0.0/8" OR src_ip="172.16.0.0/12" OR src_ip="192.168.0.0/16")
| stats count by src_ip, dest_ip, dest_port, _time
| eval risk=if(dest_port=20256, "CRITICAL-Unitronics", "HIGH-ICS")
| sort -count
```

### Splunk — VisiLogic Process Execution (Windows EDR)
```spl
index=windows EventCode=4688
(Image="*VisiLogic*" OR Image="*UniLogic*" OR CommandLine="*VisiLogic*")
| stats count by Computer, User, Image, CommandLine, _time
| where NOT User IN ("authorized_ot_admin1", "authorized_ot_admin2")
```

### Splunk — Telegram C2 Beaconing from ICS Network
```spl
index=network src_ip IN (ics_subnet_list)
(dest_ip="149.154.160.0/20" OR dest_ip="91.108.4.0/22")
dest_port=443
| stats count by src_ip, dest_ip, _time
| where count > 5
```
*(149.154.160.0/20 and 91.108.4.0/22 are Telegram's primary IP ranges)*

---

## Reference Documents

| Document | Link |
|----------|------|
| CISA Advisory AA23-335A | https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-335a |
| DOJ Press Release (Feb 2024) | US DOJ — United States v. Lashgarian et al. |
| CISA ICS-ALERT-23-335-01 | Unitronics PLC Alert |
| EPA Water Sector Cybersecurity | EPA enforcement guidance |
| Rewards for Justice | rewardsforjustice.net — $10M bounty |
| CVE-2023-6448 | NVD entry |

---

*IOC file created: 2026-05-10 | Analyst: C3PO | Grade: A1*
*Cross-reference: CISA AA23-335A, DOJ indictment Feb 2024, Unit 42, Recorded Future ICS reports*
