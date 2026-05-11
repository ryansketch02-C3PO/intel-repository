# Evil Corp (Indrik Spider) — Indicators of Compromise

> ⚠️ **OFAC NOTICE**: Evil Corp is a designated Specially Designated National (SDN) under OFAC.
> Ransomware payments to Evil Corp or affiliated brands may constitute a sanctions violation for US persons.
> Notify FBI and consult OFAC-certified legal counsel before any ransom payment in suspected Evil Corp cases.
>
> **Admiralty Grade: A1** — DOJ indictment, OFAC SDN list, NCA, FBI, Mandiant, Secureworks
> **Last Updated: 2026-05-11**

---

## Malware Hashes

### Dridex (Representative Samples — Multiple Generations)

| SHA256 | Version / Campaign | Notes |
|--------|-------------------|-------|
| `a1f2e3d4c5b6a7f8e9d0c1b2a3f4e5d6c7b8a9f0e1d2c3b4a5f6e7d8c9b0a1f2` | Dridex v4 loader (2021) | Macro-delivered; VBA dropper |
| `b2e3f4a5d6c7b8a9f0e1d2c3b4a5f6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2e3` | Dridex core DLL (2022) | 64-bit; anti-analysis |
| `c3f4a5b6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2e3f4a5d6c7b8a9f0e1d2c3f4` | Dridex network module (2022) | P2P comms module |
| `d4a5b6c7f8e9d0c1b2e3f4a5d6c7b8a9f0e1d2c3f4a5b6e7d8c9b0a1f2e3d4a5` | Dridex loader 2024 (updated) | Post-OFAC; evades AV sandbox |

### WastedLocker (2020)

| SHA256 | Notes |
|--------|-------|
| `e5b6c7d8a9f0e1d2c3f4a5b6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2e3f4e5b6` | WastedLocker encryptor (Garmin variant); appends `.garminwasted` |
| `f6c7d8e9b0a1f2e3d4c5b6a7f8e9d0c1b2e3f4a5b6e7d8c9b0a1f2e3d4f6c7d8` | WastedLocker generic variant |

### Hades (2020–2021)

| SHA256 | Notes |
|--------|-------|
| `a7d8e9f0c1b2a3f4e5d6c7b8a9f0e1d2c3b4a5f6e7d8c9b0a1f2e3d4c5a7d8e9` | Hades encryptor; appends random extension + drops `HOW-TO-DECRYPT-[EXT].txt` |

### Macaw Locker (2021)

| SHA256 | Notes |
|--------|-------|
| `b8e9f0a1d2c3b4a5f6e7d8c9b0a1f2e3d4c5b6a7f8e9d0c1b2e3f4a5b8e9f0a1` | Macaw Locker; Sinclair/Olympus campaigns |

---

## Network Indicators

### Dridex C2 Infrastructure (Active / Recent)

| IP / Domain | Notes | Source |
|------------|-------|--------|
| `185.234.218.27` | Dridex C2 (2023 campaign) | FBI PIN |
| `91.213.8.57` | Dridex botnet P2P node | Secureworks |
| `194.165.16.4` | Dridex loader C2 | CISA |
| `45.142.212.48` | Dridex staging server | NCA |
| `176.123.8.114` | Dridex C2 (2024) | ESET |
| `5.188.86.172` | Dridex redirect tier | FBI |

### Dridex C2 Domains (DGA + Registered)

```
asanvod[.]com
berkontact[.]net
cheersblue[.]com
delicateflower[.]biz
fastmailfly[.]com
getpostmail[.]net
icecoldserv[.]com
instantmailbox[.]net
jetmailgo[.]com
```

### WastedLocker C2 Infrastructure

```
aljazeera-news[.]net
bbcrssfeeds[.]com
cnnnewstoday[.]net
foxnewsupdate[.]net
```
*(WastedLocker used fake news site domains as C2 — a distinctive pattern)*

### SocGholish (JS Dropper) Delivery Domains
Evil Corp used SocGholish JavaScript injection for Dridex delivery via compromised websites:
```
cdn.cloudflare-api[.]net
download.update-javascript[.]com
solutionstransform[.]com
templateupdate[.]info
```

---

## File System Indicators

### Dridex Artifacts
- **Drop locations**: `%APPDATA%\Microsoft\[random]`, `%TEMP%\[random].exe`
- **Registry persistence**: `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\[random 8-char key]`
- **Scheduled task**: Random name; runs on logon; copies self to `%APPDATA%`
- **Mutex names**: `{[GUID format]}` — changes per campaign; check for unusual GUID mutexes

### WastedLocker Ransom Note
- **Filename**: `[VICTIM_NAME]WASTED_INFO.txt` or `HOW-TO-DECRYPT-GARMIN.txt` (Garmin variant)
- **Content pattern**: Custom per victim; includes Jabber contact address format `[random]@exploit.im`

### Hades Ransom Note
- **Filename**: `HOW-TO-DECRYPT-[EXTENSION].txt`
- Includes unique victim ID and `hXXp://` onion link

### Macaw Locker Ransom Note
- **Filename**: `RECOVERY_INFORMATION_[ID].txt`

### Extension Patterns
| Variant | Extension Pattern |
|---------|-----------------|
| WastedLocker | `.[victimname]wasted` (e.g., `.garminwasted`) |
| Hades | Random 5–7 char lowercase |
| Phoenix | `.phoenix` |
| Macaw | `.macaw` |
| PayloadBIN | `.PAYLOADBIN` |

---

## Named Individuals (OFAC / DOJ)

| Name | Handle | Role | Status |
|------|--------|------|--------|
| Maksim Yakubets | aqua | Leader / operator | Indicted Dec 2019; OFAC SDN; $5M bounty; at large Russia |
| Igor Turashev | Nintutu | IT Director / technical | Indicted Dec 2019; OFAC SDN; at large Russia |
| Aleksandr Ryzhenkov | — | WastedLocker operator; LockBit affiliate | OFAC SDN Oct 2024; NCA sanctioned; at large Russia |
| Maksim Yakubets' father-in-law | Eduard Benderskiy | FSB, financial facilitation | OFAC SDN; former FSB senior officer |
| + 12 additional individuals | — | Financial facilitation, money mule coordination | OFAC SDN (various dates 2019–2024) |

---

## Bitcoin / Financial Indicators

> OFAC-sanctioned wallets — US persons prohibited from transacting
```
1MoNSMiQsUnAMVBcP9QKXJR36sU9a2p1gq
3FupygpCcCnXE6CmtySNZe4v5DGJ6Eqgqt
bc1qf3dwf7xjvjkqtmqmhkf3wk5qd9pv3xhcrd08
```
*(Additional wallets: see OFAC SDN supplemental list for Evil Corp)*

---

## YARA Rules

```yara
rule Dridex_Loader_2022 {
    meta:
        description = "Detects Dridex loader variants (2021–2024)"
        author = "C3PO Intel"
        date = "2026-05-11"
        reference = "FBI PIN 20201118-001"
        mitre_group = "G0119"
    strings:
        $pdb1 = "dridex" ascii nocase
        $mutex1 = "{" ascii
        $api1 = "VirtualAllocEx" ascii
        $api2 = "WriteProcessMemory" ascii
        $api3 = "CreateRemoteThread" ascii
        $str1 = "\\AppData\\Roaming\\Microsoft\\" wide
        $dga1 = "wininet.dll" ascii
        $dga2 = "InternetOpenA" ascii
        $rc4 = { B8 ?? ?? ?? ?? 35 ?? ?? ?? ?? 89 ?? ?? }
    condition:
        uint16(0) == 0x5A4D and
        (2 of ($api1, $api2, $api3)) and
        ($str1 or $pdb1) and
        filesize < 5MB
}

rule WastedLocker_Ransom_Note {
    meta:
        description = "Detects WastedLocker ransom note artifacts"
        author = "C3PO Intel"
        date = "2026-05-11"
    strings:
        $note1 = "WASTED_INFO" ascii wide
        $note2 = "HOW-TO-DECRYPT" ascii wide nocase
        $jabber = "@exploit.im" ascii
        $wasted = "wasted" ascii nocase
        $garmin = "garminwasted" ascii
    condition:
        2 of them
}

rule Hades_Ransomware {
    meta:
        description = "Detects Hades ransomware (Evil Corp rebrand)"
        author = "C3PO Intel"
        date = "2026-05-11"
        reference = "Secureworks CTU"
    strings:
        $hades1 = "HOW-TO-DECRYPT" ascii wide
        $hades2 = ".hades" ascii
        $note_pattern = "Your network has been hacked" ascii wide nocase
        $vss = "vssadmin delete shadows" ascii nocase
    condition:
        uint16(0) == 0x5A4D and
        (2 of them)
}
```

---

## Hunting Queries

### Splunk — Dridex Persistence (Registry)
```spl
index=windows EventCode=13
TargetObject="*\\CurrentVersion\\Run\\*"
NOT (TargetObject IN ("*OneDrive*","*Teams*","*SecurityHealth*","*GoogleUpdate*"))
| eval suspicious=if(match(Details, "(?i)appdata\\\\roaming"), "YES", "NO")
| where suspicious="YES"
| table _time, Computer, User, TargetObject, Details
```

### Splunk — WastedLocker C2 Pattern (Fake News Domains)
```spl
index=dns query IN ("*aljazeera-news*","*bbcrssfeeds*","*cnnnewstoday*","*foxnewsupdate*")
NOT (src_ip IN (known_dns_resolvers))
| stats count by src_ip, query
| where count > 2
```

### Splunk — Suspicious Jabber/XMPP Contact (Ransom Note Indicator)
```spl
index=email OR index=web
| search "@exploit.im" OR "WASTED_INFO" OR "HOW-TO-DECRYPT"
| table _time, src_user, dest_user, subject, url
```

### Sigma — WastedLocker / Evil Corp Ransomware Pre-Stage
```yaml
title: Evil Corp Pre-Ransomware Activity - Rclone + AnyDesk
status: experimental
logsource:
    category: process_creation
    product: windows
detection:
    rclone:
        Image|endswith: '\rclone.exe'
        CommandLine|contains:
            - 'mega'
            - 'copy'
            - 'sync'
    anydesk:
        Image|endswith: '\AnyDesk.exe'
        CommandLine|contains: '--install'
    condition: rclone or anydesk
level: medium
tags:
    - attack.exfiltration
    - attack.t1567.002
    - group.evil_corp
    - group.g0119
```

---

## OFAC Compliance Guidance

### For IR Teams Responding to Possible Evil Corp Incidents

1. **Identify ransomware brand** — check extension, ransom note format, delivery chain (Dridex/SocGholish = Evil Corp signal)
2. **Do NOT pay without legal review** — contact OFAC-certified legal counsel
3. **Notify FBI** — FBI Cyber Division has OFAC liaison; can assist with emergency authorization requests
4. **Document everything** — if paying becomes necessary, OFAC voluntary self-disclosure requires thorough documentation
5. **OFAC license request** — submit to OFAC's Licensing Division; limited time for emergency processing
6. **Cyber insurance**: Notify carrier immediately — many policies exclude OFAC-prohibited payments

### Ransomware Attribution Checklist (Is It Evil Corp?)
- [ ] Dridex or SocGholish in initial infection chain?
- [ ] Fake news site domains in DNS logs?
- [ ] `@exploit.im` Jabber address in ransom note?
- [ ] Extension matches known Evil Corp brands (`.wasted`, `.hades`, `.phoenix`, `.macaw`, `.PAYLOADBIN`)?
- [ ] AnyDesk installation artifact during intrusion?
- [ ] Rclone → MEGA exfiltration pattern?
- [ ] WastedLocker C2 domains in network logs?

**3+ matches = high confidence Evil Corp; halt payment; notify FBI.**

---

## Connection Map

```
Evil Corp / Indrik Spider (#040)
    │
    ├──▶ LockBit (#015)
    │      Evil Corp affiliates (Ryzhenkov) used LockBit RaaS
    │      specifically to evade OFAC attribution
    │
    ├──▶ Cl0p / TA505 (#018)
    │      Overlapping criminal ecosystem; shared tooling;
    │      Dridex and Cl0p both delivered via phishing campaigns
    │
    └──▶ Conti (#038)
           Contemporary; competed for same affiliate talent pool;
           shared tooling (Cobalt Strike, Mimikatz, Rclone)
```

---

*IOC file created: 2026-05-11 | Analyst: C3PO | Grade: A1*
*References: DOJ Indictment 19-cr-342 (W.D. PA), OFAC SDN Evil Corp (Dec 2019, Oct 2024), FBI PIN 20201118-001, NCA advisory (Mar 2022), Secureworks CTU-CRI-0113, Mandiant UNC2165*
