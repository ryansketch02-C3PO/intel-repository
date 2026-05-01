# IOCs — Qilin Ransomware / Agenda (Scoundrel #031)

> Sources: Ransomware.live, Thrive CIRT (April 2026), Orange Cyberdefense (April 2026), Cisco Talos/GuidePoint
> TLP: WHITE | Last updated: 2026-05-01

---

## Network Indicators (IPs / FTP)

| Indicator | Type | Notes |
|---|---|---|
| `176.113.115.97` | C2 / Exfil IP | Observed exfil FTP server |
| `176.113.115.209` | C2 / Exfil IP | Observed exfil FTP server |
| `85.209.11.49` | C2 IP | Qilin infrastructure |
| `31.41.244.100` | C2 IP | Qilin infrastructure |
| `188.119.66.189` | C2 IP | Qilin infrastructure |
| `ftp://dataShare:nX4aJxu3rYUMiLjCMtuJYTKS@176.113.115.97` | FTP credential | Exfil endpoint — observed in post-compromise activity |
| `ftp://dataShare:2bTWYKNn7aK7Rqp9mnv3@176.113.115.209` | FTP credential | Exfil endpoint — observed in post-compromise activity |

## Leak Site (TOR)
| Indicator | Type | Notes |
|---|---|---|
| `ozsxj4hwxub7gio347ac7tyqqozvfioty37skqilzo2oqfs4cw2mgtyd.onion` | TOR leak site | Primary Qilin leak site |
| `kbsqoivihgdmwczmxkbovk7ss2dcynitwhhfu5yw725dboqo5kthfaad.onion` | TOR (DDoS protection) | Backup/protected endpoint |

---

## File Hashes (MD5 — Qilin Encryptor / Tools)

| MD5 | Notes |
|---|---|
| `d6e7547ad7dfd1fbc62e8282aebcc391` | Qilin payload |
| `f588802958c35fe18eb87bc36651a3d1` | Qilin payload |
| `2bb209ccfc5103eccab523c875050cfa` | Qilin payload |
| `a7e7d00d531cb7ca27d0f3bee448573f` | Qilin payload |
| `964c13b68dc6b6b918b66a9a10469d2a` | Qilin payload |
| `3b10127e65fa3e215d21e0a2e7fd32be` | Qilin payload |
| `d1c331c17ddd4abe0d53755461c1ec9a` | Qilin payload |
| `417ad60624345ef85e648038e18902ab` | Qilin payload |
| `b04e8ee43aba85fa5c585b9335c953c2` | Qilin payload |
| `59d756280b06cf113ca43abc0050edd5` | Qilin payload |
| `88bb86494cb9411a9692f9c8e67ed32c` | Qilin payload |
| `37155f0bca29ccd6b6d4f5b2bc42eb4d` | Qilin payload |
| `e01776ec67b9f1ae780c3e24ecc4bf06` | Qilin payload |
| `11d795baafa44b73766e850d13b8e254` | Qilin payload |
| `88630916b0c6633ca28c8896416a93ee` | Qilin payload |
| `dd42c3e017889c107a81da78d87dc8af` | Qilin payload |
| `1c4bea81c0da22badd9b7eab574c51cd` | Qilin payload |
| `ab05a1925fee8334a2114811d5283364` | Qilin payload |
| `64a590760fdbb84356544cc90ac3d50f` | Qilin payload |
| `2020979e080d7ac9c0403172573c7de8` | Qilin payload |

---

## File System Indicators

| Indicator | Type | Notes |
|---|---|---|
| `README-RECOVER-[victim_extension].txt` | Ransom note | Dropped in every encrypted directory; extension is unique per victim |
| `[unique 8-char extension]` appended to encrypted files | Encrypted file extension | Generated per engagement — no static extension |
| Controller binary (single `.exe`, ~10–15MB) | Qilin controller | Large size due to embedded PsExec, PowerShell, VMware modules |
| PSEXESVC service created on remote hosts | Lateral movement artifact | Created during PsExec-based spread in Phase 4 |

---

## Registry Indicators

| Key / Value | Notes |
|---|---|
| `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\[name]` = `[controller.exe] --password [value]` | Persistence mechanism — Qilin registers controller with embedded password for reboot persistence; password in plaintext in registry value |

---

## Process / Command Indicators (YARA / Sigma Focus)

| Indicator | Description | Confidence |
|---|---|---|
| `vssadmin.exe delete shadows /all /quiet` | VSS deletion — Phase 2 | 🔴 HIGH |
| `vssadmin.exe delete shadows /all` + silent execution | VSS deletion variant | 🔴 HIGH |
| `fsutil behavior set SymlinkEvaluation R2L:1` | SMB symlink enable — Phase 2 | 🔴 HIGH |
| `wevtutil cl [log]` or PowerShell `Clear-EventLog` in loop | Event log clearing — Phase 2 continuous loop | 🔴 HIGH |
| PsExec staging to `ADMIN$` share from single host across multiple targets | Phase 4 Windows spread | 🔴 HIGH |
| PSEXESVC service installation on remote hosts | Phase 4 Windows spread | 🔴 HIGH |
| `Import-Module VMware.PowerCLI` on non-VMware-admin host | Phase 4 ESXi spread | 🔴 HIGH |
| `Import-Module Posh-SSH` on non-VMware-admin host | Phase 4 ESXi spread | 🔴 HIGH |
| `Connect-VIServer` + `Get-VMHost` + `Set-SCPItem` sequence | ESXi fleet enumeration + payload upload | 🔴 HIGH |
| TSM-SSH enablement via PowerCLI | ESXi SSH enable for payload execution | 🔴 HIGH |
| `execInstalledOnly` bypass via PowerCLI | ESXi execution restriction disable | 🔴 HIGH |
| `README-RECOVER-*.txt` created in bulk across directories | Active encryption in progress | 🔴 HIGH (active incident) |
| Bulk file extension change (>100 files / 30s, same new extension) | Active encryption | 🔴 HIGH (active incident) |
| `upd.exe` (Carbon Black Cloud Sensor updater) outside CBDefense path | BYOVD EDR killer component | 🔴 HIGH |
| Zemana Anti-Rootkit driver loaded by non-security process | BYOVD component | 🔴 HIGH |
| Toshiba power management driver loaded outside Toshiba software | BYOVD component | 🟡 MEDIUM |

---

## YARA Rules (Thrive CIRT, April 2026)

```yara
rule Qilin_Controller_Fingerprint {
    meta:
        description = "Qilin ransomware controller — main binary"
        author = "Thrive CIRT (via C3PO Intel)"
        date = "2026-04"
    strings:
        $fam = "qilin" nocase
        $ext = "README-RECOVER-" nocase
        $rsa = "RSA" nocase
        $aes = "AES-CTR" nocase
        $cha = "ChaCha20" nocase
        $psx = "psexec" nocase
        $vcr = "vCenter" nocase
        $esx = "ESXi" nocase
    condition:
        5 of them
}

rule Qilin_Embedded_PsExec {
    meta:
        description = "Qilin controller — embedded PsExec resource detection"
    strings:
        $svc = "PSEXESVC" ascii
        $adm = "ADMIN$" ascii
        $spr = "--spread-process" ascii
    condition:
        2 of them
}

rule Qilin_Ransom_Note {
    meta:
        description = "Qilin ransom note content"
    strings:
        $hdr = "README-RECOVER-" nocase
        $tor = ".onion" nocase
        $enc = "encrypted" nocase
        $qil = "qilin" nocase
    condition:
        3 of them
}
```

---

## Sigma Rules (Thrive CIRT, April 2026)

```yaml
# Sigma: VSS deletion + event log clearing (Qilin Phase 2)
title: Qilin Phase 2 - VSS Deletion and Log Clearing
logsource: process_creation
detection:
  vss_deletion:
    CommandLine|contains:
      - 'vssadmin.exe delete shadows /all /quiet'
      - 'vssadmin delete shadows'
  log_clear:
    CommandLine|contains:
      - 'wevtutil cl'
      - 'Clear-EventLog'
  condition: vss_deletion or log_clear
level: critical

# Sigma: VMware PowerCLI spread sequence (Qilin Phase 4 ESXi)
title: Qilin Phase 4 - ESXi Spread via PowerCLI
logsource: powershell_script_block
detection:
  vmware_import:
    ScriptBlockText|contains:
      - 'Import-Module VMware.PowerCLI'
      - 'Import-Module Posh-SSH'
      - 'Connect-VIServer'
      - 'Get-VMHost'
      - 'Set-SCPItem'
      - 'execInstalledOnly'
  condition: 2 of vmware_import
  filter:
    ProcessPath|contains: 'vmware-admin-host'  # Adjust to your VMware admin host
level: critical

# Sigma: PsExec spread to admin shares (Qilin Phase 4 Windows)
title: Qilin Phase 4 - PsExec Lateral Spread
logsource: process_creation
detection:
  psexec_spread:
    Image|endswith: 'psexec.exe'
    CommandLine|contains:
      - 'ADMIN$'
      - '--spread-process'
  psexesvc_install:
    CommandLine|contains: 'PSEXESVC'
  condition: psexec_spread or psexesvc_install
level: high
```

---

## SmokedHam Affiliate (UNC2465) Indicators

> UNC2465 is a confirmed Qilin affiliate — historically DarkSide, LockBit, Hunters International
> Delivers Qilin via SmokedHam backdoor through malvertising (Google Ads posing as RVTools, Remote Desktop Manager)

| Indicator | Type | Notes |
|---|---|---|
| Malvertising domains serving fake RVTools / RDM installers | Domain | Rotate frequently; use Cloudflare Workers for domain fronting |
| AWS infrastructure endpoints in outbound C2 traffic | Network | Legitimate AWS used to blend C2 |
| PuTTY / KiTTY / Total Commander used as post-compromise tools | Process | UNC2465 uses these to blend into legitimate admin activity |
| Zoho Assist RMM installation post-compromise | Process | Persistence and RMM C2 |
| Cloudflare Workers in HTTP C2 traffic | Network | Domain fronting via legitimate Cloudflare infrastructure |

---

*IOC file created: 2026-05-01 | Author: C3PO | TLP: WHITE*
