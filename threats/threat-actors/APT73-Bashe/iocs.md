# APT73 / Bashe — IOCs

> Note: APT73 operates as a RaaS. IOCs vary by affiliate.
> APT73 has been documented recycling old breach data — validate all claimed victim data independently.

---

## Infrastructure IOCs

### Tor DLS — APT73 brand (offline mid-2025)
- `apt73grpjgjwykrenq7vnjejue76vosdzptdvmonv7vyqnsyokrw57ad.onion`
- `fleqwmg7xnanypt5km2m75l72q7nlcvlp2m4sdmgjxorsn6tb3zyp3qd.onion`
- `wn6vonooq6fggjdgyocp7bioykmfjket7sbp47cwhgubvowwd7ws5pyd.onion`

### Tor DLS — BASHE rebrand (active mid-2025)
- `basheqtvzqwz4vp6ks5lm2ocq7i6tozqgf6vjcasj4ezmsy4bkpshhyd.onion`
- `basherq53eniermxovo3bkduw5qqq5bkqcml3qictfmamgvmzovykyqd.onion`
- `basherykagbxoaiaxkgqhmhd5gbmedwb3di4ig3ouovziagosv4n77qd.onion`
- `bashete63b3gcijfofpw6fmn3rwnmyi5aclp55n6awcfbexivexbhyad.onion`
- `bashex7mokreyoxl6wlswxl4foi7okgs7or7aergnuiockuoq35yt3ad.onion`

### Clearnet
- `eraleignews.com` — associated with eraleign alias

---

## Behavioral IOCs

### Ransomware Artifacts
| Indicator | Type |
|---|---|
| `wbadmin DELETE SYSTEMSTATEBACKUP` | CLI — VSS/backup deletion |
| `wevtutil cl System` / `wevtutil cl Security` | CLI — event log clearing |
| `.apt73` or `.bashe` file extension | File — encrypted files |
| Ransom note: `README.txt` or `[ACTOR]-README.txt` | File |

### Pre-Ransomware Tradecraft
| Tool | Category |
|---|---|
| Advanced Port Scanner / SoftPerfect | Discovery |
| Cobalt Strike beacon | C2 |
| Mimikatz / LaZagne | Credential access |
| Rclone (cloud exfil config) | Exfiltration |
| PsExec / WMI | Lateral movement / execution |
| ESXi SSH targeting | Impact — VM encryption |

---

## Detection Rules

```yaml
# VSS deletion
wbadmin DELETE SYSTEMSTATEBACKUP OR vssadmin delete shadows
Severity: CRITICAL

# Mass file encryption
EventID: 4663
Pattern: High-volume rename/write with non-standard extension
Severity: CRITICAL

# Rclone exfil
Process: rclone.exe
Args: "--config" AND cloud provider
Severity: HIGH

# Event log clearing
EventID: 1102 OR 104
Severity: HIGH

# LSASS dump
TargetProcess: lsass.exe
AccessType: 0x1FFFFF OR 0x1010
CallerProcess: NOT ["MsMpEng.exe", "svchost.exe"]
Severity: HIGH
```

---

## Data Authenticity Warning

Verify all APT73 DLS listings independently before assuming breach. CloudSEK documented multiple recycled/fabricated claims in late 2024/early 2025. A listing still warrants investigation for reputational and regulatory risk even if data authenticity is unconfirmed.
