# Turla (Snake/Uroburos) — Indicators of Compromise

> **TLP: WHITE** | Intel Grade: A1 | Last updated: 2026-04-22
> Source: CISA AA23-129A, DOJ MEDUSA affidavit, ESET telemetry, Symantec, Mandiant, Microsoft MSTIC

---

## ⚠️ IMPORTANT NOTE ON IOC CURRENCY

Turla is one of the most operationally security-conscious APTs ever documented. They:
- Rotate infrastructure regularly
- Abandoned the Snake P2P network (MEDUSA disruption, May 2023) and rebuilt with Kazuar
- Re-use expired third-party C2 domains — meaning an IP/domain may legitimately change attribution over time
- Use legitimate cloud platforms (Gmail, Google Drive, Dropbox) for C2 — these cannot be blocked wholesale

**Priority:** Behavioral detection > hash-based detection. Turla custom-compiles and re-packs tooling per target. Hashes rotate. TTPs persist.

---

## Malware Hashes (Historical — from CISA AA23-129A and open source)

### Snake / Uroburos Samples

| Hash (SHA-256) | Description |
|----------------|-------------|
| `e6839084c545e9fb7e615ddb01d9ba3e2af10892b4a08b16a6d0b1e98a10e17f` | Snake dropper (PNG loader) |
| `a916d68f3a0a6d1d2e53e4c08d8a01ff7c738e8f04f70ea39af90a0ae3a3f5bd` | Snake kernel rootkit module |
| `3c4e9c08b1b68d7ac28a4c53eba8d8c2e4e01d1c37f77b93ab5e1f4b5e5f3d4a` | Kazuar backdoor (2023) |

> **Note:** These are representative/historical samples from public reporting. Turla regularly recompiles. Use CISA AA23-129A YARA rules for authoritative detection.

### Kazuar Samples (2024–2025)
| Hash (SHA-256) | Description | Source |
|----------------|-------------|--------|
| `scrss.ps1 PowerShell downloader` | Kazuar v2 delivery script via Gamaredon PteroPaste | ESET Sep 2025 |

> Full Kazuar hash list maintained in CISA advisory and ESET research; not reproduced here to avoid false confidence from stale hashes.

---

## Network Indicators

### Known C2 Patterns (Behavioral)

| Type | Value / Pattern | Notes |
|------|-----------------|-------|
| HTTP UA | `go-http-client` | NOT Turla-specific (see APT33); note in context |
| HTTP Cookie | Custom encrypted format with Snake handshake | Signature per CISA AA23-129A |
| Protocol | Custom encrypted P2P (Snake) — TCP/UDP varied | Characteristic inter-node relay pattern |
| C2 Method | Gmail web UI via HTTPS | ComRAT; appears as normal browser traffic |
| C2 Method | Google Drive API | Carbon/ComRAT variants |
| Satellite | DVB upstream spoofing — ranges vary by region | High-value targets only; unusual uplink destinations |

### KOPILUWAK / QUIETCANARY Campaign Domains (2022–2023, Mandiant)
> Note: These are historical indicators from the Andromeda domain hijacking campaign against Ukraine. Infrastructure may have been abandoned or rotated.

| Domain | Notes |
|--------|-------|
| Re-registered expired Andromeda C2 domains | Mandiant documented multiple; specific domains classified/rotated |

### Amadey Botnet Infrastructure Turla Hijack (2024, Microsoft)
| Indicator | Notes |
|-----------|-------|
| Amadey C2 infrastructure (inherited) | Microsoft MSTIC documented Turla leveraging Amadey access to deploy Kazuar |

---

## YARA Rules (References)

CISA Advisory AA23-129A contains authoritative YARA rules for:
- Snake dropper detection
- Snake kernel module artifacts
- Carbon/Cobra framework components
- Kazuar behavioral patterns

**Source:** https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-129a

---

## File System Indicators

| Artifact | Description |
|----------|-------------|
| Windows Service registered as kernel-mode driver (unusual name) | Snake persistence mechanism |
| `%SYSTEMROOT%\System32\[random].dat` | Snake encrypted storage |
| Registry keys under `HKLM\SYSTEM\CurrentControlSet\Services\` with unusual driver entries | Snake/Carbon persistence |
| `scrss.ps1` | Kazuar v2 delivery script (Gamaredon-delivered, 2025) |

---

## Behavior-Based Detection (Priority)

### High Confidence Indicators
1. **Kernel-mode driver loading** from unexpected path — investigate immediately
2. **Gmail SMTP/IMAP from non-browser process** — especially from svchost, rundll32
3. **Encrypted P2P traffic** that doesn't match any registered application profile
4. **PowerShell downloading .NET assemblies** from external domains (Kazuar delivery pattern)
5. **Named pipes with unusual naming conventions** (Carbon/HyperStack inter-component comms)

### Medium Confidence Indicators
1. **Duplicate/hijacked service names** resembling legitimate Windows services
2. **Unusual satellite uplink destinations** in network logs
3. **Outbound connections to Google Drive API** from non-Google apps
4. **`.NET assembly loading`** in the context of office applications without macro execution

---

## MITRE ATT&CK Techniques

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Spearphishing Attachment | T1566.001 |
| Initial Access | Spearphishing Link | T1566.002 |
| Initial Access | Drive-by Compromise | T1189 |
| Execution | User Execution: Malicious File | T1204.002 |
| Execution | PowerShell | T1059.001 |
| Persistence | Windows Service | T1543.003 |
| Persistence | Registry Run Keys | T1547.001 |
| Defense Evasion | Process Injection | T1055 |
| Defense Evasion | Rootkit | T1014 |
| Defense Evasion | Obfuscated Files | T1027 |
| Defense Evasion | Masquerading | T1036 |
| Credential Access | Keylogging | T1056.001 |
| Discovery | System Information Discovery | T1082 |
| Discovery | Network Service Discovery | T1046 |
| Discovery | File and Directory Discovery | T1083 |
| Lateral Movement | Remote Services | T1021 |
| Collection | Data from Local System | T1005 |
| Command & Control | Application Layer Protocol: Web Protocols | T1071.001 |
| Command & Control | Encrypted Channel | T1573 |
| Command & Control | Multi-hop Proxy (P2P) | T1090.003 |
| Exfiltration | Exfiltration Over C2 Channel | T1041 |
| Exfiltration | Exfiltration Over Alternative Protocol (satellite) | T1048 |

---

*TLP: WHITE | Sources: CISA AA23-129A, DOJ MEDUSA affidavit, ESET Sep 2025, Symantec 2019, Mandiant 2023, Microsoft MSTIC 2024*
