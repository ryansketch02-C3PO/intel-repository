# Copy Fail (CVE-2026-31431) — MITRE ATT&CK Map

> Full ATT&CK coverage for the Copy Fail exploit chain: from initial foothold through post-exploitation.
> Scope includes the LPE primitive itself, observed in-the-wild TTPs, and post-exploitation capabilities unlocked by root access.
> Last updated: 2026-05-04

---

## Navigator Layer Summary

**Total techniques mapped: 28**
**Tactics covered: 10 of 14**

| Tactic | Techniques Mapped |
|---|---|
| Initial Access | 3 |
| Execution | 3 |
| Persistence | 3 |
| Privilege Escalation | 1 |
| Defense Evasion | 6 |
| Credential Access | 3 |
| Discovery | 5 |
| Lateral Movement | 2 |
| Collection | 1 |
| Impact | 3 |

> **Note:** Command and Control and Exfiltration are not mapped here — these are attacker-specific post-exploitation choices not tied to the Copy Fail primitive itself. Observed in-the-wild exfiltration has occurred but uses attacker-controlled C2 infrastructure not attributable to Copy Fail specifically.

---

## Technique Reference

### TA0001 — Initial Access

Copy Fail is not a remote exploit — it requires an existing unprivileged foothold. The following represent observed and probable initial access vectors that precede Copy Fail deployment.

---

#### T1078 — Valid Accounts
**Sub-technique:** T1078.003 — Local Accounts (post-escalation); T1078.004 — Cloud Accounts (cloud scanning context)

| Field | Detail |
|---|---|
| **Relevance** | Primary observed initial access vector |
| **In-the-Wild** | ✅ Confirmed — Huntress documented SSH/SSLVPN credential compromise preceding Copy Fail in hands-on-keyboard intrusions |
| **Notes** | Attacker uses stolen credentials to gain unprivileged shell access; Copy Fail then escalates to root. Cloud scanning activity (Microsoft Defender telemetry, May 1) suggests automated credential-stuffing into cloud SSH endpoints followed by Copy Fail deployment. |

---

#### T1190 — Exploit Public-Facing Application

| Field | Detail |
|---|---|
| **Relevance** | High-probability initial access in web server / containerised app context |
| **In-the-Wild** | ⚠️ Not confirmed specifically with Copy Fail — inferred from attack surface |
| **Notes** | Web app RCE → unprivileged container shell → Copy Fail for container escape is the highest-risk Kubernetes scenario. Microsoft Defender specifically calls out "malicious CI job execution" and "container footholds" as observed precursors. |

---

#### T1133 — External Remote Services

| Field | Detail |
|---|---|
| **Relevance** | SSH, VPN, RDP (Linux xrdp) |
| **In-the-Wild** | ✅ Confirmed — SSLVPN compromised credential used as entry in documented intrusion |
| **Notes** | Any remote service that drops a shell as a non-root user qualifies as a Copy Fail delivery mechanism. |

---

### TA0002 — Execution

---

#### T1059.006 — Command and Scripting Interpreter: Python

| Field | Detail |
|---|---|
| **Relevance** | The primary public PoC is a ~732-byte Python script using only standard library modules (`os`, `socket`, `zlib`) |
| **In-the-Wild** | ✅ Confirmed — Microsoft Defender signature `Exploit:Python/CopyFail.A` is actively detecting this |
| **Notes** | Python 3 is present by default on virtually all modern Linux distributions. No compilation, no pip dependencies, no network access required. The script is executed directly from the unprivileged user's shell. A C version and Go version also exist in the wild. |

---

#### T1106 — Native API

| Field | Detail |
|---|---|
| **Relevance** | Core of the exploit primitive |
| **In-the-Wild** | ✅ Confirmed — the exploit chain uses only standard Linux syscalls |
| **Notes** | The exploit uses: `socket(AF_ALG, SOCK_SEQPACKET, 0)`, `setsockopt`, `bind`, `splice()`, `sendmsg()`, `recvmsg()`, and `execve()`. No kernel modules, no unusual capabilities, no special privileges required. All calls are valid from standard user context — the kernel is tricked, not forced. |

---

#### T1611 — Escape to Host *(Container Context)*

| Field | Detail |
|---|---|
| **Relevance** | Copy Fail is a container escape primitive — the Linux page cache is shared between containers and the host kernel |
| **In-the-Wild** | ⚠️ Demonstrated in Theori PoC; not yet confirmed in observed wild incidents specifically |
| **Notes** | Because the page cache is shared at the kernel level, a process inside a container can corrupt the page cache of a file on the host. This means a compromised container process with no special capabilities can escalate to root on the host node — bypassing the container boundary entirely. Kubernetes nodes are at meaningful risk. |

---

### TA0003 — Persistence

Post-root, the following persistence mechanisms become available.

---

#### T1543.002 — Create or Modify System Process: Systemd Service

| Field | Detail |
|---|---|
| **Relevance** | Root access enables creating persistent systemd services |
| **In-the-Wild** | ✅ Confirmed post-exploitation — backdoor deployment observed by Microsoft Defender |
| **Notes** | Attacker creates a systemd unit file under `/etc/systemd/system/` to maintain persistence across reboots. Requires root — which Copy Fail provides. |

---

#### T1098 — Account Manipulation

| Field | Detail |
|---|---|
| **Relevance** | Root access enables adding SSH keys, modifying `/etc/sudoers`, creating new accounts |
| **In-the-Wild** | ⚠️ Probable — consistent with observed post-exploitation activity pattern |
| **Notes** | A common root-level persistence step: add attacker SSH public key to `/root/.ssh/authorized_keys` or create a new privileged user account for backdoor re-entry. |

---

#### T1053.003 — Scheduled Task/Job: Cron

| Field | Detail |
|---|---|
| **Relevance** | Root-level cron for persistent payload execution |
| **In-the-Wild** | ⚠️ Probable |
| **Notes** | Root access to `/etc/cron.d/` or `/var/spool/cron/` enables persistent scheduled execution invisible to standard users. |

---

### TA0004 — Privilege Escalation

---

#### T1068 — Exploitation for Privilege Escalation ⭐ *Core Technique*

| Field | Detail |
|---|---|
| **Relevance** | This IS Copy Fail. The entire vulnerability is an LPE primitive. |
| **In-the-Wild** | ✅ Confirmed |
| **CVE** | CVE-2026-31431 |
| **CVSS** | 7.8 HIGH (`AV:L/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`) |
| **Notes** | Unprivileged local user (or container process) → root / UID 0. Requires: (1) AF_ALG module loaded, (2) `algif_aead` available, (3) a setuid binary readable by the user (e.g., `/usr/bin/su`). No race condition, no timing window, no crashes. Deterministic. |

---

### TA0005 — Defense Evasion

Copy Fail's stealth profile is exceptional. Multiple evasion characteristics are inherent to the exploit — not optional add-ons.

---

#### T1211 — Exploitation for Defense Evasion

| Field | Detail |
|---|---|
| **Relevance** | The page cache poisoning primitive evades all disk-based defenses by design |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | The exploit modifies only the kernel's in-memory page cache. The on-disk binary is never touched. File integrity monitoring tools (AIDE, Tripwire, Wazuh syscheck, osquery file events) all hash on-disk files and will report the binary as clean even after successful exploitation. |

---

#### T1070.004 — Indicator Removal: File Deletion

| Field | Detail |
|---|---|
| **Relevance** | Exploit staging files are typically deleted after use |
| **In-the-Wild** | ✅ Confirmed pattern — exploit script removed from disk after execution |
| **Notes** | The 732-byte PoC script is commonly deleted immediately after root is obtained, leaving no persistent file artifact. Memory-only modifications revert on reboot. |

---

#### T1036 — Masquerading

| Field | Detail |
|---|---|
| **Relevance** | The EICAR trigger string is stored reversed in the binary to avoid static detection |
| **In-the-Wild** | ✅ Confirmed — technique documented in Theori PoC source |
| **Notes** | The exploit binary contains the EICAR test string in reversed form, flipped to its correct form at runtime only when needed to trigger Defender's scanner. This defeats static AV scanning of the exploit binary itself. |

---

#### T1027 — Obfuscated Files or Information

| Field | Detail |
|---|---|
| **Relevance** | Reversed EICAR + minimal script footprint |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Beyond the reversed EICAR, the 732-byte script has no obvious exploit signatures — it uses only standard system calls any legitimate program might make. No shellcode, no ROP chains, no mmap tricks. Static analysis yields nothing useful. |

---

#### T1562.001 — Impair Defenses: Disable or Modify Tools

| Field | Detail |
|---|---|
| **Relevance** | Post-root: attacker can disable SELinux, AppArmor, auditd, Falco |
| **In-the-Wild** | ✅ Confirmed — security control disabling observed post-exploitation (Microsoft Defender, May 1) |
| **Notes** | Once root is obtained via Copy Fail, the attacker can `setenforce 0`, stop auditd, remove Falco rules, or modify `/etc/selinux/config`. This is a post-escalation action, not a property of the exploit itself, but it is an observed TTP in the wild. |

---

#### T1222.002 — File and Directory Permissions Modification: Linux and Mac File and Directory Permissions

| Field | Detail |
|---|---|
| **Relevance** | Root allows chmod/chown on any file |
| **In-the-Wild** | ⚠️ Probable post-exploitation step |
| **Notes** | With root, attacker can chmod system binaries, modify `/etc/passwd`, or remove file immutability flags (`chattr -i`) to prepare for further tampering. |

---

### TA0006 — Credential Access

---

#### T1003.008 — OS Credential Dumping: /etc/passwd and /etc/shadow

| Field | Detail |
|---|---|
| **Relevance** | Root access → direct `/etc/shadow` read |
| **In-the-Wild** | ✅ Confirmed — credential exfiltration observed post-exploitation |
| **Notes** | Standard post-root credential harvest. `/etc/shadow` is readable only by root. Copy Fail delivers root; credential dump follows trivially. Hashes can then be cracked offline or used in pass-the-hash-equivalent attacks. |

---

#### T1552.004 — Unsecured Credentials: Private Keys

| Field | Detail |
|---|---|
| **Relevance** | Root access → read all SSH private keys, service account tokens, API keys |
| **In-the-Wild** | ✅ Confirmed — data exfiltration observed |
| **Notes** | Includes `/root/.ssh/id_*`, all user `~/.ssh/id_*`, Kubernetes service account tokens at `/var/run/secrets/kubernetes.io/serviceaccount/token`, cloud metadata credentials, `.env` files, `~/.aws/credentials`, etc. |

---

#### T1552.007 — Unsecured Credentials: Container API

| Field | Detail |
|---|---|
| **Relevance** | Container context — Kubernetes service account tokens, Docker socket |
| **In-the-Wild** | ⚠️ High probability in Kubernetes deployments |
| **Notes** | After escaping a container via Copy Fail, the attacker gains access to the node's kubelet, which can be used to list and exec into all pods on the node. Service account tokens with cluster-admin bindings are a direct path to full cluster compromise. |

---

### TA0007 — Discovery

---

#### T1082 — System Information Discovery

| Field | Detail |
|---|---|
| **Relevance** | Kernel version check is the first step — required to confirm exploitability |
| **In-the-Wild** | ✅ Confirmed — automated scanning for kernel version observed |
| **Notes** | `uname -r` output is readable by any unprivileged user, including from within containers. This single command tells the attacker whether the target is vulnerable. Microsoft Defender observed automated scanning of cloud instances specifically checking kernel versions. |

---

#### T1613 — Container and Resource Discovery

| Field | Detail |
|---|---|
| **Relevance** | Container context — enumerate host and sibling containers after escape |
| **In-the-Wild** | ⚠️ High probability in Kubernetes/container deployments |
| **Notes** | After container escape, attacker uses kubelet API (`http://localhost:10250/pods`) or Docker socket to enumerate all pods, images, secrets, and config maps on the node without additional credentials. |

---

#### T1083 — File and Directory Discovery

| Field | Detail |
|---|---|
| **Relevance** | Pre-exploit: locate setuid binaries to use as corruption target |
| **In-the-Wild** | ✅ Confirmed — exploit targets `/usr/bin/su` by default but scans for alternatives |
| **Notes** | `find / -perm -4000 -type f 2>/dev/null` — standard setuid binary enumeration. The exploit needs a readable setuid binary; `/usr/bin/su` is the default target but `/usr/bin/passwd`, `/usr/bin/newgrp`, etc. are alternatives. This command is executable by any unprivileged user. |

---

#### T1087 — Account Discovery

| Field | Detail |
|---|---|
| **Relevance** | Post-root — enumerate all users, service accounts, privileged groups |
| **In-the-Wild** | ✅ Confirmed — `whoami`, `id`, `cat /etc/passwd` observed in intrusion chains |
| **Notes** | Standard post-exploitation enumeration. With root, attacker can also read locked accounts, service accounts, and LDAP/PAM configuration that would be inaccessible to standard users. |

---

#### T1057 — Process Discovery

| Field | Detail |
|---|---|
| **Relevance** | Pre/post-exploit — identify running security tools, EDR agents, detection processes |
| **In-the-Wild** | ⚠️ Probable |
| **Notes** | Attacker checks for Falco, auditd, osquery, CrowdStrike Falcon sensor, Sysdig agent, etc. before or after running the exploit. Identifies targets for T1562 (impair defenses) if root is obtained. |

---

### TA0008 — Lateral Movement

---

#### T1210 — Exploitation of Remote Services

| Field | Detail |
|---|---|
| **Relevance** | Root + harvested credentials → pivot to adjacent systems |
| **In-the-Wild** | ✅ Confirmed — lateral movement prep observed post-exploitation |
| **Notes** | With `/etc/shadow` hashes or SSH keys harvested post-escalation, attacker can move laterally to other servers in the same network. In cloud environments, instance metadata credentials (AWS IAM role tokens, Azure managed identity) become available at root. |

---

#### T1021.004 — Remote Services: SSH

| Field | Detail |
|---|---|
| **Relevance** | Primary lateral movement mechanism post-credential harvest |
| **In-the-Wild** | ✅ Confirmed pattern |
| **Notes** | SSH private keys readable after root escalation enable passwordless lateral movement to all servers that trust those keys — including bastion hosts, deployment servers, and database hosts. |

---

### TA0009 — Collection

---

#### T1005 — Data from Local System

| Field | Detail |
|---|---|
| **Relevance** | Root access → read any file on the system |
| **In-the-Wild** | ✅ Confirmed — data exfiltration observed post-exploitation |
| **Notes** | Covers: source code, configuration files, database dumps, secrets, API keys, certificates, logs, and any data inaccessible to unprivileged users. In CI/CD runner context, this includes build artifacts and signing keys. |

---

### TA0040 — Impact

---

#### T1496 — Resource Hijacking

| Field | Detail |
|---|---|
| **Relevance** | Cryptomining is a common low-effort post-root outcome in cloud scanning campaigns |
| **In-the-Wild** | ⚠️ Probable — consistent with automated cloud scanning pattern |
| **Notes** | Automated cloud scanning + Copy Fail LPE → root → cryptominer deployment is a known opportunistic playbook. Root access enables bypassing CPU/resource quotas that would otherwise limit miner output. |

---

#### T1486 — Data Encrypted for Impact

| Field | Detail |
|---|---|
| **Relevance** | Ransomware deployment enabled by root access |
| **In-the-Wild** | ⚠️ Not yet confirmed for Copy Fail specifically — high-probability future use |
| **Notes** | Copy Fail as an LPE step in a ransomware kill chain is a natural evolution: initial foothold (phishing/RCE) → Copy Fail (root) → ransomware deployment with full filesystem access. Copy Fail is more capable than Dirty Pipe for this use case due to broader platform coverage. |

---

#### T1485 — Data Destruction

| Field | Detail |
|---|---|
| **Relevance** | Root enables wiping of logs, backups, and system state |
| **In-the-Wild** | ⚠️ Probable in state-sponsored / destructive attack scenarios |
| **Notes** | With root, attacker can `rm -rf /var/log/*`, overwrite backup directories, destroy database files, and remove audit trails. In critical infrastructure or hacktivist contexts, destructive impact is a real risk. |

---

## Technique Quick-Reference Table

| ATT&CK ID | Technique | Sub-technique | Tactic | In Wild | Confidence |
|---|---|---|---|---|---|
| T1078 | Valid Accounts | .003 / .004 | Initial Access | ✅ | 🔴 HIGH |
| T1190 | Exploit Public-Facing Application | — | Initial Access | ⚠️ | 🟡 MEDIUM |
| T1133 | External Remote Services | — | Initial Access | ✅ | 🔴 HIGH |
| T1059.006 | Command and Scripting: Python | — | Execution | ✅ | 🔴 HIGH |
| T1106 | Native API | — | Execution | ✅ | 🔴 HIGH |
| T1611 | Escape to Host | — | Execution / Priv Esc | ⚠️ | 🔴 HIGH |
| T1543.002 | Create/Modify System Process: Systemd | — | Persistence | ✅ | 🔴 HIGH |
| T1098 | Account Manipulation | — | Persistence | ⚠️ | 🟡 MEDIUM |
| T1053.003 | Scheduled Task: Cron | — | Persistence | ⚠️ | 🟡 MEDIUM |
| T1068 | Exploitation for Privilege Escalation | — | Privilege Escalation | ✅ | 🔴 HIGH |
| T1211 | Exploitation for Defense Evasion | — | Defense Evasion | ✅ | 🔴 HIGH |
| T1070.004 | Indicator Removal: File Deletion | — | Defense Evasion | ✅ | 🔴 HIGH |
| T1036 | Masquerading | — | Defense Evasion | ✅ | 🔴 HIGH |
| T1027 | Obfuscated Files or Information | — | Defense Evasion | ✅ | 🔴 HIGH |
| T1562.001 | Impair Defenses: Disable/Modify Tools | — | Defense Evasion | ✅ | 🔴 HIGH |
| T1222.002 | File/Dir Permissions Modification: Linux | — | Defense Evasion | ⚠️ | 🟡 MEDIUM |
| T1003.008 | OS Credential Dumping: /etc/shadow | — | Credential Access | ✅ | 🔴 HIGH |
| T1552.004 | Unsecured Credentials: Private Keys | — | Credential Access | ✅ | 🔴 HIGH |
| T1552.007 | Unsecured Credentials: Container API | — | Credential Access | ⚠️ | 🔴 HIGH |
| T1082 | System Information Discovery | — | Discovery | ✅ | 🔴 HIGH |
| T1613 | Container and Resource Discovery | — | Discovery | ⚠️ | 🔴 HIGH |
| T1083 | File and Directory Discovery | — | Discovery | ✅ | 🔴 HIGH |
| T1087 | Account Discovery | — | Discovery | ✅ | 🔴 HIGH |
| T1057 | Process Discovery | — | Discovery | ⚠️ | 🟡 MEDIUM |
| T1210 | Exploitation of Remote Services | — | Lateral Movement | ✅ | 🔴 HIGH |
| T1021.004 | Remote Services: SSH | — | Lateral Movement | ✅ | 🔴 HIGH |
| T1005 | Data from Local System | — | Collection | ✅ | 🔴 HIGH |
| T1496 | Resource Hijacking | — | Impact | ⚠️ | 🟡 MEDIUM |
| T1486 | Data Encrypted for Impact | — | Impact | ⚠️ | 🟡 MEDIUM |
| T1485 | Data Destruction | — | Impact | ⚠️ | 🟡 MEDIUM |

**Legend:** ✅ Confirmed in-the-wild | ⚠️ Probable / demonstrated but not confirmed in Copy Fail-specific incidents

---

## ATT&CK Navigator JSON

The following can be imported directly into [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) at `https://mitre-attack.github.io/attack-navigator/`.

```json
{
  "name": "CVE-2026-31431 Copy Fail",
  "versions": { "attack": "16", "navigator": "5.1.0", "layer": "4.5" },
  "domain": "enterprise-attack",
  "description": "MITRE ATT&CK coverage for CVE-2026-31431 (Copy Fail) — Linux kernel LPE via algif_aead page cache poisoning. Includes LPE primitive, observed ITW TTPs, and post-exploitation capabilities. Last updated 2026-05-04.",
  "filters": { "platforms": ["Linux", "Containers"] },
  "sorting": 0,
  "layout": { "layout": "side", "aggregateFunction": "average", "showID": true, "showName": true },
  "hideDisabled": false,
  "techniques": [
    { "techniqueID": "T1078.003", "tactic": "initial-access",       "color": "#ff6666", "comment": "Stolen SSH/SSLVPN credentials used as precursor foothold — confirmed ITW", "enabled": true, "score": 100 },
    { "techniqueID": "T1078.004", "tactic": "initial-access",       "color": "#ff9999", "comment": "Cloud account credential stuffing observed in automated scanning campaigns", "enabled": true, "score": 75 },
    { "techniqueID": "T1190",     "tactic": "initial-access",       "color": "#ffcc99", "comment": "Web app RCE → container shell → Copy Fail — high-probability container escape vector", "enabled": true, "score": 60 },
    { "techniqueID": "T1133",     "tactic": "initial-access",       "color": "#ff6666", "comment": "SSLVPN entry confirmed in documented hands-on-keyboard intrusion", "enabled": true, "score": 100 },
    { "techniqueID": "T1059.006", "tactic": "execution",            "color": "#ff6666", "comment": "732-byte Python PoC — standard library only; Exploit:Python/CopyFail.A detected in wild", "enabled": true, "score": 100 },
    { "techniqueID": "T1106",     "tactic": "execution",            "color": "#ff6666", "comment": "AF_ALG socket + splice() + sendmsg/recvmsg — all standard unprivileged syscalls", "enabled": true, "score": 100 },
    { "techniqueID": "T1611",     "tactic": "execution",            "color": "#ff9999", "comment": "Page cache shared across container boundaries — container escape to host root demonstrated", "enabled": true, "score": 85 },
    { "techniqueID": "T1543.002", "tactic": "persistence",          "color": "#ff6666", "comment": "Backdoor deployment via systemd unit confirmed post-exploitation (Microsoft Defender, May 1)", "enabled": true, "score": 100 },
    { "techniqueID": "T1098",     "tactic": "persistence",          "color": "#ffcc99", "comment": "SSH key insertion / new account creation probable post-root", "enabled": true, "score": 60 },
    { "techniqueID": "T1053.003", "tactic": "persistence",          "color": "#ffcc99", "comment": "Root cron for persistent payload execution — probable post-exploitation step", "enabled": true, "score": 60 },
    { "techniqueID": "T1068",     "tactic": "privilege-escalation", "color": "#cc0000", "comment": "CORE TECHNIQUE — CVE-2026-31431. Unprivileged user → UID 0 via algif_aead page cache write. Deterministic, no race condition.", "enabled": true, "score": 100 },
    { "techniqueID": "T1211",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "Page cache only — on-disk file unchanged; FIM tools (AIDE/Tripwire/Wazuh) see nothing", "enabled": true, "score": 100 },
    { "techniqueID": "T1070.004", "tactic": "defense-evasion",      "color": "#ff6666", "comment": "Exploit script deleted from disk immediately after root obtained", "enabled": true, "score": 100 },
    { "techniqueID": "T1036",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "EICAR string stored reversed at compile time; flipped to active form at runtime only", "enabled": true, "score": 100 },
    { "techniqueID": "T1027",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "732-byte script; standard syscalls only; no shellcode; no static signatures", "enabled": true, "score": 100 },
    { "techniqueID": "T1562.001", "tactic": "defense-evasion",      "color": "#ff6666", "comment": "Post-root disabling of SELinux/AppArmor/auditd/Falco confirmed (Microsoft Defender May 1)", "enabled": true, "score": 100 },
    { "techniqueID": "T1222.002", "tactic": "defense-evasion",      "color": "#ffcc99", "comment": "chmod/chattr on system files after root — probable post-escalation step", "enabled": true, "score": 60 },
    { "techniqueID": "T1003.008", "tactic": "credential-access",    "color": "#ff6666", "comment": "/etc/shadow readable as root; credential exfiltration confirmed post-exploitation", "enabled": true, "score": 100 },
    { "techniqueID": "T1552.004", "tactic": "credential-access",    "color": "#ff6666", "comment": "SSH private keys, Kubernetes tokens, .aws/credentials, .env files — all readable as root", "enabled": true, "score": 100 },
    { "techniqueID": "T1552.007", "tactic": "credential-access",    "color": "#ff9999", "comment": "Kubernetes service account tokens + kubelet API accessible after container escape", "enabled": true, "score": 85 },
    { "techniqueID": "T1082",     "tactic": "discovery",            "color": "#ff6666", "comment": "uname -r readable by any user; automated kernel version scanning confirmed (Microsoft Defender)", "enabled": true, "score": 100 },
    { "techniqueID": "T1613",     "tactic": "discovery",            "color": "#ff9999", "comment": "Post-escape kubelet enumeration of pods/secrets/configmaps on node", "enabled": true, "score": 85 },
    { "techniqueID": "T1083",     "tactic": "discovery",            "color": "#ff6666", "comment": "Setuid binary enumeration (find / -perm -4000) required to identify exploit target", "enabled": true, "score": 100 },
    { "techniqueID": "T1087",     "tactic": "discovery",            "color": "#ff6666", "comment": "whoami/id/cat /etc/passwd confirmed in intrusion chains preceding Copy Fail deployment", "enabled": true, "score": 100 },
    { "techniqueID": "T1057",     "tactic": "discovery",            "color": "#ffcc99", "comment": "Process enumeration to identify EDR/detection tools pre/post exploit", "enabled": true, "score": 60 },
    { "techniqueID": "T1210",     "tactic": "lateral-movement",     "color": "#ff6666", "comment": "Root + harvested credentials → pivot; lateral movement prep confirmed post-exploitation", "enabled": true, "score": 100 },
    { "techniqueID": "T1021.004", "tactic": "lateral-movement",     "color": "#ff6666", "comment": "SSH private keys harvested post-root enable passwordless lateral movement", "enabled": true, "score": 100 },
    { "techniqueID": "T1005",     "tactic": "collection",           "color": "#ff6666", "comment": "Data exfiltration confirmed post-exploitation; root enables reading all files on system", "enabled": true, "score": 100 },
    { "techniqueID": "T1496",     "tactic": "impact",               "color": "#ffcc99", "comment": "Cryptomining probable in automated cloud scanning campaigns — consistent with observed pattern", "enabled": true, "score": 60 },
    { "techniqueID": "T1486",     "tactic": "impact",               "color": "#ffcc99", "comment": "Ransomware deployment via Copy Fail LPE — not yet confirmed; high-probability future use", "enabled": true, "score": 60 },
    { "techniqueID": "T1485",     "tactic": "impact",               "color": "#ffcc99", "comment": "Data destruction / log wiping possible post-root; relevant in destructive attack scenarios", "enabled": true, "score": 60 }
  ],
  "gradient": { "colors": ["#ffffff", "#ff6666"], "minValue": 0, "maxValue": 100 },
  "legendItems": [
    { "label": "Confirmed in-the-wild (score 100)", "color": "#ff6666" },
    { "label": "High probability / demonstrated (score 75-85)", "color": "#ff9999" },
    { "label": "Probable / inferred (score 60)", "color": "#ffcc99" }
  ],
  "metadata": [
    { "name": "CVE", "value": "CVE-2026-31431" },
    { "name": "Vulnerability", "value": "Copy Fail" },
    { "name": "CVSS", "value": "7.8 HIGH" },
    { "name": "Platform", "value": "Linux Kernel 4.14 – 7.0-rc" },
    { "name": "Author", "value": "C3PO" },
    { "name": "Last Updated", "value": "2026-05-04" }
  ]
}
```

---

*Created: 2026-05-04 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
