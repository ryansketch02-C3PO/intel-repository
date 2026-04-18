# Payouts King — Indicators of Compromise

> **Scoundrel #021** | C3PO Threat Actor Repository  
> Sources: Zscaler ThreatLabz (Apr 2026), Sophos STAC4713 (Nov 2025), ransomware.live, RedPiranha (Dec 2025)  
> TLP: CLEAR

---

## ⚠️ Usage Note

Payouts King is an **active and evolving** threat as of early 2026. No government advisory (CISA/FBI) has been issued as of profile creation date. IOCs should be treated as **B2 confidence** (credible commercial threat intel, multiple independent sources). Cross-reference with current threat feeds before deploying for active blocking.

---

## Network Indicators

### Leak Site (Tor .onion)
```
payoutsgn7cy6uliwevdqspncjpfxpmzgirwl2au65la7rfs5x3qnbqd.onion
```
*Last observed active: April 16, 2026 (ransomware.live check)*

### Negotiation Channel
| Type | Indicator |
|---|---|
| `tox` | `535F403A2EA2DC71A392E18D7DB77FEF70845C0B7E5B9114CD30D301870304379C3547E324E2` |

*Tox ID used for victim negotiations. Victims directed here via ransom note.*

---

## File System Artifacts

### Encrypted File Extension
```
.payoutsking
```
*Appended to all encrypted files*

### Ransom Note Filenames
```
PAYOUTS-README.txt     (primary; dropped in multiple directories)
readme_locker.txt      (variant; found on victim desktops)
```

### Encrypted File Header Magic Bytes
```
CRPT    (4-byte magic value at start of encrypted file structure)
```
*Encrypted file format: AES-encrypted data + RSA-encrypted file encryption parameters; 487-byte structured footer*

### Temporary Encryption Files
```
[original_filename].tmp    (56-byte structure; created when --temp flag used during encryption)
```

---

## Behavioral / Host Indicators

### Process Termination
- Directly terminates **131 hardcoded AV/EDR processes** using direct syscalls (bypasses standard API hooks)
- Builds runtime syscall table by walking `ntdll` export table — avoids userland hooks entirely

### Safe Mode Boot Persistence
```
bcdedit /set {current} safeboot network
bcdedit /set {current} safeboot minimal
```
*Forces system into safe mode to reduce security tool load during encryption*

### Backup Targeting
```
vssadmin delete shadows /all /quiet     (shadow copy deletion)
net stop "Veeam Backup Service"
net stop "BackupExec*"
wmic shadowcopy delete
```

### Post-Encryption Cleanup
- Clears Windows Event Logs
- Wipes Recycle Bin
- Deletes temporary encryption files

### DLL Sideload (Feb 2026 variant)
| Legitimate Binary | Malicious Payload | Notes |
|---|---|---|
| `ADNotificationManager.exe` | `vcruntime140_1.dll` | Sideloads Havoc C2 beacon |

---

## Execution Guard (Anti-Sandbox)

Payouts King **will not execute** without a specific command-line identity parameter:
- The CRC checksum of the parameter must match a hardcoded expected value
- CRC polynomial: **0xBDC65592** (custom, defeats precomputed hash tables)
- Without the correct parameter: **no encryption occurs**

*This is a strong sandbox/automated analysis bypass. Dynamic analysis requires correct execution parameters.*

---

## String Obfuscation Fingerprint

- **Stack-based QWORD array string construction** — strings built at runtime from encrypted fragments
- **FNV1 hash resolution with unique per-string seeds** — defeats precomputed API hash lookup databases
- Combined XOR + AES decryption for individual string values

*Use memory-based YARA rules or dynamic analysis (with correct CLI parameter) for detection.*

---

## Infrastructure (STAC4713 Campaign, Nov 2025)

| Indicator Type | Details |
|---|---|
| **QEMU abuse** | QEMU used to host hidden VM with reverse SSH backdoor for covert C2 |
| **Tunnel type** | Reverse SSH via QEMU serial/network interface — traffic invisible to host EDR |
| **Lateral movement pivot** | C2 traffic routed through QEMU guest; harvested domain creds forwarded |

*Sophos Sophos STAC4713 report contains additional QEMU-specific network IOCs.*

---

## Exfiltration Tools

| Tool | Use | Notes |
|---|---|---|
| Rclone | Exfiltration to SFTP (Feb–Mar 2026) | Attacker-controlled SFTP server; replaced Tor-based exfil in later campaigns |
| Tor .onion infrastructure | Primary exfil channel (2025) | Data staged to leak site servers |

---

## CVEs of Note

| CVE | Product | Use |
|---|---|---|
| *No confirmed zero-days attributed as of Dec 2025* | — | Group relies on stolen credentials and social engineering over exploit-based access |
| ConnectWise CVE-2024-1709 | ConnectWise ScreenConnect | Possibly inherited from Black Basta affiliate playbook; not confirmed for PayoutsKing specifically |
| Cisco SSL VPN (unspecified) | Cisco SSL VPN | Exploited in Feb 2026 incident for initial access |

---

## Mitigations

1. **Disable Quick Assist for non-IT users** — primary delivery mechanism for Teams-based social engineering attacks
2. **Train users on spam bombing + fake IT helpdesk calls** — this is the primary initial access chain
3. **Monitor Microsoft Teams for external user contact** — block or flag messages from unrecognized external tenants impersonating IT
4. **Monitor for bcdedit safe mode modifications** — `bcdedit /set safeboot` is a near-certain ransomware precursor
5. **Protect Veeam and BackupExec services** — specifically targeted for termination; use tamper protection
6. **Block QEMU execution** in environments where virtualization tools aren't authorized — especially on endpoints
7. **Alert on ADNotificationManager.exe** loading unexpected DLLs
8. **Monitor Rclone** for unauthorized exfil to external SFTP
9. **Offline air-gapped backups** — on-premise backups will be targeted
10. **MFA on all VPN/remote access** — credential stuffing is a primary access vector
11. **Hunt for `.payoutsking` extension** and `PAYOUTS-README.txt` in endpoint/file integrity monitoring

---

*IOCs last updated: 2026-04-17 | Author: C3PO*  
*Sources: Zscaler ThreatLabz, Sophos STAC4713, ransomware.live, RedPiranha Threat Intel*
