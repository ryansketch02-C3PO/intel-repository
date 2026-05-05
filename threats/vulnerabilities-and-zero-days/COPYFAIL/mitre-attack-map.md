# Copy Fail (CVE-2026-31431) — MITRE ATT&CK Map

> Full ATT&CK coverage for the Copy Fail exploit chain: from initial foothold through post-exploitation.
> Scope includes the LPE primitive itself, observed in-the-wild TTPs, and post-exploitation capabilities unlocked by root access.
> Last updated: 2026-05-05

---

## Source Index

Sources referenced throughout this document. Cited inline by **[S#]**.

| # | Source | Date | URL |
|---|---|---|---|
| S1 | Theori / Xint — Copy Fail technical disclosure + PoC | Apr 29, 2026 | https://xint.io/blog/copy-fail-linux-distributions |
| S2 | Theori — GitHub PoC (`theori-io/copy-fail-CVE-2026-31431`) | Apr 29, 2026 | https://github.com/theori-io/copy-fail-CVE-2026-31431 |
| S3 | Sysdig TRT — CVE-2026-31431 analysis + Falco rule | Apr 30, 2026 | https://www.sysdig.com/blog/cve-2026-31431-copy-fail-linux-kernel-flaw-lets-local-users-gain-root-in-seconds |
| S4 | Microsoft Defender Security Research — CVE-2026-31431 analysis | May 1, 2026 | https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/ |
| S5 | CISA Known Exploited Vulnerabilities Catalog — CVE-2026-31431 | May 1, 2026 | https://www.cisa.gov/known-exploited-vulnerabilities-catalog |
| S6 | BleepingComputer — New Linux 'Copy Fail' flaw gives hackers root on major distros | Apr 30, 2026 | https://www.bleepingcomputer.com/news/security/new-linux-copy-fail-flaw-gives-hackers-root-on-major-distros/ |
| S7 | Huntress — in-the-wild intrusion analysis (via BleepingComputer / Microsoft) | Apr 30, 2026 | https://www.huntress.com/blog/cve-2026-31431-copy-fail |
| S8 | XM Cyber — Copy Fail active exploitation analysis | May 3, 2026 | https://www.xmcyber.com/blog/copy-fail-cve-2026-31431-active-exploitation/ |
| S9 | Percivalll — Kubernetes container escape PoC | May 5, 2026 | https://github.com/Percivalll/Copy-Fail-CVE-2026-31431-Kubernetes-PoC |
| S10 | NVD — CVE-2026-31431 | Apr 22, 2026 | https://nvd.nist.gov/vuln/detail/CVE-2026-31431 |
| S11 | GHSA-2274-3hgr-wxv6 — GitHub Security Advisory | Apr 22, 2026 | https://github.com/advisories/GHSA-2274-3hgr-wxv6 |
| S12 | CloudSEK — RedSun/Copy Fail: When Defender Becomes the Attacker | Apr 30, 2026 | https://www.cloudsek.com/blog/copy-fail-cve-2026-31431 |
| S13 | CERT-EU Security Advisory 2026-005 | Apr 29, 2026 | https://cert.europa.eu/publications/security-advisories/2026-005/ |
| S14 | rootsecdev — second public PoC (`rootsecdev/cve_2026_31431`) | May 5, 2026 | https://github.com/rootsecdev/cve_2026_31431 |
| S15 | Linux kernel fix commit `a664bf3d603d` | Apr 1, 2026 | https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a664bf3d603d |
| S16 | Linux kernel regression commit `72548b093ee3` (root cause) | Jul 2017 | https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=72548b093ee3 |

---

## Navigator Layer Summary

**Total techniques mapped: 30**
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
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Attacker uses stolen credentials to gain unprivileged shell access; Copy Fail then escalates to root. Cloud scanning activity suggests automated credential-stuffing into cloud SSH endpoints followed by Copy Fail deployment. |
| **Sources** | **[S7]** Huntress documented SSH/SSLVPN credential compromise preceding Copy Fail in hands-on-keyboard intrusions. **[S4]** Microsoft Defender: "Secure Shell (SSH) access... as observed precursors" to exploitation. **[S6]** BleepingComputer: "initial access vector such as... SSH access" cited in attack chain. |

---

#### T1190 — Exploit Public-Facing Application

| Field | Detail |
|---|---|
| **Relevance** | High-probability initial access in web server / containerised app context |
| **In-the-Wild** | ⚠️ Not confirmed specifically with Copy Fail — inferred from attack surface |
| **Notes** | Web app RCE → unprivileged container shell → Copy Fail for container escape is the highest-risk Kubernetes scenario. Microsoft Defender specifically calls out "malicious CI job execution" and "container footholds" as observed precursors. |
| **Sources** | **[S4]** Microsoft Defender: *"container footholds"* and *"malicious CI job execution"* explicitly cited as observed initial access vectors preceding Copy Fail deployment. **[S13]** CERT-EU: recommends Kubernetes nodes and CI/CD runners as highest-priority hardening targets. |

---

#### T1133 — External Remote Services

| Field | Detail |
|---|---|
| **Relevance** | SSH, VPN, RDP (Linux xrdp) |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Any remote service that drops a shell as a non-root user qualifies as a Copy Fail delivery mechanism. |
| **Sources** | **[S7]** Huntress: SSLVPN compromised credential used as entry point in the confirmed April 30 hands-on-keyboard intrusion. **[S4]** Microsoft Defender: SSH access cited as observed precursor. |

---

### TA0002 — Execution

---

#### T1059.006 — Command and Scripting Interpreter: Python

| Field | Detail |
|---|---|
| **Relevance** | The primary public PoC is a ~732-byte Python script using only standard library modules (`os`, `socket`, `zlib`) |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Python 3 is present by default on virtually all modern Linux distributions. No compilation, no pip dependencies, no network access required. The script is executed directly from the unprivileged user's shell. C and Go variants also exist in the wild. |
| **Sources** | **[S2]** Theori PoC: Python script using only `os`, `socket`, `zlib` from stdlib — no external dependencies. **[S4]** Microsoft Defender: active detection signature `Exploit:Python/CopyFail.A` confirms Python PoC execution observed in the wild. **[S1]** Xint: *"a 732-byte Python script that roots every Linux distribution shipped since 2017."* |

---

#### T1106 — Native API

| Field | Detail |
|---|---|
| **Relevance** | Core of the exploit primitive |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | The exploit uses: `socket(AF_ALG, SOCK_SEQPACKET, 0)`, `setsockopt`, `bind`, `splice()`, `sendmsg()`, `recvmsg()`, and `execve()`. No kernel modules, no unusual capabilities, no special privileges required. All calls are valid from standard user context — the kernel is tricked, not forced. |
| **Sources** | **[S1]** Xint technical disclosure: complete syscall chain documented — `AF_ALG socket → splice() → sendmsg/recvmsg`. **[S3]** Sysdig TRT: full technical breakdown of `AF_ALG`, `SOCK_SEQPACKET`, and `splice()` interaction. **[S4]** Microsoft Defender: *"The script does not rely on networking, compilation, or third-party libraries, making it ideal for execution in restricted containers."* **[S15]** Linux kernel fix commit confirms the specific code path involved. |

---

#### T1611 — Escape to Host *(Container Context)*

| Field | Detail |
|---|---|
| **Relevance** | Copy Fail is a container escape primitive — the Linux page cache is shared between containers and the host kernel |
| **In-the-Wild** | ✅ Demonstrated — public Kubernetes PoC confirmed working May 5 |
| **Notes** | Because the page cache is shared at the kernel level, a process inside a standard (non-rootless) container can corrupt the page cache of a file on the host. A compromised container process with no special capabilities can escalate to root on the host node — bypassing the container boundary entirely. **Note:** In rootless containers (Podman/Docker rootless with User Namespaces), escalation is scoped within the User Namespace only and does not yield host root. |
| **Sources** | **[S9]** Percivalll Kubernetes PoC (May 5): *"pod-to-host root escalation confirmed working on vanilla unpatched cluster in under 10 seconds."* **[S4]** Microsoft Defender: *"the vulnerability also enables cross-container impacts and container escape scenarios."* **[S1]** Xint: *"The page cache is shared across all processes on a system including across container boundaries."* **[S8]** XM Cyber: active exploitation in cloud/Kubernetes environments confirmed. |

---

### TA0003 — Persistence

Post-root, the following persistence mechanisms become available.

---

#### T1543.002 — Create or Modify System Process: Systemd Service

| Field | Detail |
|---|---|
| **Relevance** | Root access enables creating persistent systemd services |
| **In-the-Wild** | ✅ Confirmed post-exploitation |
| **Notes** | Attacker creates a systemd unit file under `/etc/systemd/system/` to maintain persistence across reboots. Requires root — which Copy Fail provides. |
| **Sources** | **[S4]** Microsoft Defender: *"backdoor deployment"* listed as confirmed post-exploitation activity observed in Microsoft Defender telemetry following Copy Fail escalation. **[S8]** XM Cyber: post-exploitation activity including persistence mechanisms observed in active exploitation campaigns. |

---

#### T1098 — Account Manipulation

| Field | Detail |
|---|---|
| **Relevance** | Root access enables adding SSH keys, modifying `/etc/sudoers`, creating new accounts |
| **In-the-Wild** | ⚠️ Probable — consistent with observed post-exploitation activity pattern |
| **Notes** | A common root-level persistence step: add attacker SSH public key to `/root/.ssh/authorized_keys` or create a new privileged user account for backdoor re-entry. |
| **Sources** | **[S4]** Microsoft Defender: post-exploitation phase described includes account-level persistence as part of "lateral movement" activity observed post-escalation. **[S7]** Huntress intrusion analysis: post-SYSTEM activity consistent with this pattern. |

---

#### T1053.003 — Scheduled Task/Job: Cron

| Field | Detail |
|---|---|
| **Relevance** | Root-level cron for persistent payload execution |
| **In-the-Wild** | ⚠️ Probable |
| **Notes** | Root access to `/etc/cron.d/` or `/var/spool/cron/` enables persistent scheduled execution invisible to standard users. |
| **Sources** | **[S4]** Microsoft Defender: general post-exploitation activity pattern; persistence mechanisms following root escalation noted. Standard post-root playbook consistent with observed attacker behavior in cloud scanning campaigns **[S8]**. |

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
| **Notes** | Unprivileged local user (or container process) → root / UID 0. Requires: (1) AF_ALG module loaded or built-in, (2) `algif_aead` available, (3) a setuid binary readable by the user (e.g., `/usr/bin/su`). No race condition, no timing window, no crashes. Deterministic. 100% reliable on confirmed-affected distributions. |
| **Sources** | **[S1]** Xint: root technical disclosure, root cause analysis, full exploit chain. **[S10]** NVD: CVE-2026-31431, CVSS 7.8 HIGH. **[S5]** CISA KEV: listed May 1, federal deadline May 15 — confirms active exploitation. **[S4]** Microsoft Defender: *"The exploit is deterministic, does not rely on race conditions."* **[S3]** Sysdig TRT: independent technical analysis confirming reliability and scope. **[S7]** Huntress: live exploitation documented April 30. **[S15]** Linux kernel fix commit `a664bf3d603d`. **[S16]** Root-cause regression commit `72548b093ee3` (July 2017). |

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
| **Sources** | **[S1]** Xint: *"The corrupted page is never marked dirty by the kernel's writeback machinery. Standard file integrity tools comparing on-disk checksums will miss it because the on-disk file is unchanged."* **[S3]** Sysdig TRT: *"only the in-memory page cache is corrupted"* — explicitly flags FIM bypass. **[S4]** Microsoft Defender: *"This corruption occurs entirely within the kernel, bypassing traditional user-space protections."* **[S6]** BleepingComputer: stealth characteristic highlighted as key differentiator from Dirty Pipe. |

---

#### T1070.004 — Indicator Removal: File Deletion

| Field | Detail |
|---|---|
| **Relevance** | Exploit staging files are typically deleted after use |
| **In-the-Wild** | ✅ Confirmed pattern |
| **Notes** | The 732-byte PoC script is commonly deleted immediately after root is obtained, leaving no persistent file artifact. Memory-only modifications revert on reboot. |
| **Sources** | **[S3]** Sysdig TRT: exploit staging and cleanup behavior documented; notes that memory modifications revert on reboot leaving no disk trace. **[S4]** Microsoft Defender: *"Phase 5"* of attack chain includes cleanup to avoid detection; *"This completes the transition... At this point, kernel trust boundaries are broken, SELinux/AppArmor protections are effectively neutralized."* |

---

#### T1036 — Masquerading

| Field | Detail |
|---|---|
| **Relevance** | The EICAR trigger string is stored reversed in the binary to avoid static detection |
| **In-the-Wild** | ✅ Confirmed — technique documented in Theori PoC source |
| **Notes** | The exploit binary contains the EICAR test string in reversed form, flipped to its correct form at runtime only when needed to trigger Defender's scanner. This defeats static AV scanning of the exploit binary itself. |
| **Sources** | **[S2]** Theori PoC source code: EICAR string stored reversed at compile time, flipped at runtime — explicitly documented in repository comments. **[S12]** CloudSEK analysis: *"exploit stored reversed to evade static analysis"* noted in technical breakdown. **[S3]** Sysdig TRT: documents the EICAR obfuscation as a deliberate static-detection evasion technique. |

---

#### T1027 — Obfuscated Files or Information

| Field | Detail |
|---|---|
| **Relevance** | Reversed EICAR + minimal script footprint |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Beyond the reversed EICAR, the 732-byte script has no obvious exploit signatures — it uses only standard system calls any legitimate program might make. No shellcode, no ROP chains, no mmap tricks. Static analysis yields nothing useful. |
| **Sources** | **[S1]** Xint: *"The script does not rely on networking, compilation, or third-party libraries, making it ideal for execution in restricted containers and hardened environments."* **[S2]** Theori PoC: source demonstrates minimal observable footprint — standard `os`, `socket`, `zlib` imports only. **[S4]** Microsoft Defender: *"The script does not rely on networking, compilation, or third-party libraries"* — confirms no signatures to detect statically. |

---

#### T1562.001 — Impair Defenses: Disable or Modify Tools

| Field | Detail |
|---|---|
| **Relevance** | Post-root: attacker can disable SELinux, AppArmor, auditd, Falco |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Once root is obtained via Copy Fail, the attacker can `setenforce 0`, stop auditd, remove Falco rules, or modify `/etc/selinux/config`. This is a post-escalation action, not a property of the exploit itself, but it is an observed TTP in active exploitation. |
| **Sources** | **[S4]** Microsoft Defender: *"security control disabling"* explicitly listed as confirmed post-exploitation activity observed in telemetry following Copy Fail escalation. *"SELinux/AppArmor protections are effectively neutralized"* once UID 0 is achieved. **[S8]** XM Cyber: post-exploitation security control modification observed in active campaigns. |

---

#### T1222.002 — File and Directory Permissions Modification: Linux and Mac File and Directory Permissions

| Field | Detail |
|---|---|
| **Relevance** | Root allows chmod/chown on any file |
| **In-the-Wild** | ⚠️ Probable post-exploitation step |
| **Notes** | With root, attacker can chmod system binaries, modify `/etc/passwd`, or remove file immutability flags (`chattr -i`) to prepare for further tampering. |
| **Sources** | **[S4]** Microsoft Defender: post-exploitation phase includes modification of system state; root-level system manipulation observed. Standard post-root playbook consistent with observed attacker behavior **[S7]**. |

---

### TA0006 — Credential Access

---

#### T1003.008 — OS Credential Dumping: /etc/passwd and /etc/shadow

| Field | Detail |
|---|---|
| **Relevance** | Root access → direct `/etc/shadow` read |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Standard post-root credential harvest. `/etc/shadow` is readable only by root. Copy Fail delivers root; credential dump follows trivially. Hashes can then be cracked offline or used in pass-the-hash-equivalent attacks. |
| **Sources** | **[S4]** Microsoft Defender: *"credential and data exfiltration"* confirmed as observed post-exploitation activity. **[S7]** Huntress: credential harvesting observed in documented post-SYSTEM intrusion chain. **[S8]** XM Cyber: credential exfiltration noted in active exploitation analysis. |

---

#### T1552.004 — Unsecured Credentials: Private Keys

| Field | Detail |
|---|---|
| **Relevance** | Root access → read all SSH private keys, service account tokens, API keys |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Includes `/root/.ssh/id_*`, all user `~/.ssh/id_*`, Kubernetes service account tokens at `/var/run/secrets/kubernetes.io/serviceaccount/token`, cloud metadata credentials, `.env` files, `~/.aws/credentials`, etc. |
| **Sources** | **[S4]** Microsoft Defender: *"credential and data exfiltration"* confirmed. Cloud metadata credential access specifically noted: *"cloud SaaS running user code"* identified as priority target. **[S8]** XM Cyber: data exfiltration including credentials observed in active cloud exploitation. **[S6]** BleepingComputer: *"data center blast radius"* framing — all apps, servers, databases on compromised host accessible. |

---

#### T1552.007 — Unsecured Credentials: Container API

| Field | Detail |
|---|---|
| **Relevance** | Container context — Kubernetes service account tokens, Docker socket |
| **In-the-Wild** | ⚠️ High probability in Kubernetes deployments |
| **Notes** | After escaping a container via Copy Fail, the attacker gains access to the node's kubelet, which can be used to list and exec into all pods on the node. Service account tokens with cluster-admin bindings are a direct path to full cluster compromise. |
| **Sources** | **[S9]** Percivalll Kubernetes PoC: demonstrates full node access including kubelet API after container escape. **[S4]** Microsoft Defender: *"millions of Kubernetes clusters"* identified as affected; *"container escape scenarios"* explicitly described. **[S1]** Xint: *"a Kubernetes node compromise vector"* — container escape primitive explicitly named. |

---

### TA0007 — Discovery

---

#### T1082 — System Information Discovery

| Field | Detail |
|---|---|
| **Relevance** | Kernel version check is the first step — required to confirm exploitability |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | `uname -r` output is readable by any unprivileged user, including from within containers. This single command tells the attacker whether the target is vulnerable. Automated scanning of cloud instances specifically checks kernel versions. |
| **Sources** | **[S4]** Microsoft Defender: *"Phase 1: reconnaissance — kernel version information is easily obtainable from within containers and user namespaces and does not require elevated privileges."* Automated scanning for vulnerable kernel versions confirmed in Microsoft Defender telemetry. **[S1]** Xint PoC: `uname -r` as documented first step of exploit chain. **[S8]** XM Cyber: confirms automated kernel version scanning in observed exploitation campaigns. |

---

#### T1613 — Container and Resource Discovery

| Field | Detail |
|---|---|
| **Relevance** | Container context — enumerate host and sibling containers after escape |
| **In-the-Wild** | ⚠️ High probability in Kubernetes/container deployments |
| **Notes** | After container escape, attacker uses kubelet API (`http://localhost:10250/pods`) or Docker socket to enumerate all pods, images, secrets, and config maps on the node without additional credentials. |
| **Sources** | **[S9]** Percivalll Kubernetes PoC: demonstrates node-level resource enumeration following container escape. **[S4]** Microsoft Defender: *"Because containers share the host kernel, a single vulnerable kernel version immediately expands the impact radius from one container to the entire node."* **[S1]** Xint: *"Copy Fail is not just a local privilege escalation. It is a container escape primitive and a Kubernetes node compromise vector."* |

---

#### T1083 — File and Directory Discovery

| Field | Detail |
|---|---|
| **Relevance** | Pre-exploit: locate setuid binaries to use as corruption target |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | `find / -perm -4000 -type f 2>/dev/null` — standard setuid binary enumeration. The exploit needs a readable setuid binary; `/usr/bin/su` is the default target but `/usr/bin/passwd`, `/usr/bin/newgrp`, etc. are alternatives. This command is executable by any unprivileged user. |
| **Sources** | **[S1]** Xint: explicitly targets `/usr/bin/su` as the default setuid binary; discusses alternative targets. **[S2]** Theori PoC: setuid binary targeting is a core step in the published exploit code. **[S3]** Sysdig TRT: documents setuid binary targeting as the mechanism that converts the 4-byte write into a root shell. **[S4]** Microsoft Defender: *"exploiting... setuid binaries (for example, /usr/bin/su)"* confirmed in attack chain description. |

---

#### T1087 — Account Discovery

| Field | Detail |
|---|---|
| **Relevance** | Post-root — enumerate all users, service accounts, privileged groups |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Standard post-exploitation enumeration. With root, attacker can also read locked accounts, service accounts, and LDAP/PAM configuration that would be inaccessible to standard users. |
| **Sources** | **[S7]** Huntress: `whoami /priv`, `cmdkey /list`, `net group` and AD structure mapping confirmed in the documented hands-on-keyboard intrusion chain preceding Copy Fail deployment. **[S4]** Microsoft Defender: reconnaissance including privilege and account enumeration documented in Phase 1 of observed attack chains. |

---

#### T1057 — Process Discovery

| Field | Detail |
|---|---|
| **Relevance** | Pre/post-exploit — identify running security tools, EDR agents, detection processes |
| **In-the-Wild** | ⚠️ Probable |
| **Notes** | Attacker checks for Falco, auditd, osquery, CrowdStrike Falcon sensor, Sysdig agent, etc. before or after running the exploit. Identifies targets for T1562 (impair defenses) if root is obtained. |
| **Sources** | **[S4]** Microsoft Defender: Phase 1 reconnaissance includes scanning for security tooling. **[S3]** Sysdig TRT: Falco detection rule specifically designed to catch the AF_ALG SEQPACKET socket — implies attackers may probe for Falco presence before execution. Standard post-root playbook consistent with observed campaigns **[S8]**. |

---

### TA0008 — Lateral Movement

---

#### T1210 — Exploitation of Remote Services

| Field | Detail |
|---|---|
| **Relevance** | Root + harvested credentials → pivot to adjacent systems |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | With `/etc/shadow` hashes or SSH keys harvested post-escalation, attacker can move laterally to other servers in the same network. In cloud environments, instance metadata credentials (AWS IAM role tokens, Azure managed identity) become available at root. |
| **Sources** | **[S4]** Microsoft Defender: *"lateral movement within shared environments"* explicitly confirmed as observed post-exploitation activity. *"Lateral movement to other systems on the same network"* cited in attack chain. **[S7]** Huntress: *"credential enumeration, AD recon, lateral movement prep"* documented in post-escalation phase of confirmed intrusion. |

---

#### T1021.004 — Remote Services: SSH

| Field | Detail |
|---|---|
| **Relevance** | Primary lateral movement mechanism post-credential harvest |
| **In-the-Wild** | ✅ Confirmed pattern |
| **Notes** | SSH private keys readable after root escalation enable passwordless lateral movement to all servers that trust those keys — including bastion hosts, deployment servers, and database hosts. |
| **Sources** | **[S7]** Huntress: SSH-based lateral movement documented in post-escalation phase. **[S4]** Microsoft Defender: SSH access used both as initial access and lateral movement vector in observed campaigns. **[S4]** Microsoft Defender: node rotation and credential invalidation recommended specifically because SSH key material is exposed post-root. |

---

### TA0009 — Collection

---

#### T1005 — Data from Local System

| Field | Detail |
|---|---|
| **Relevance** | Root access → read any file on the system |
| **In-the-Wild** | ✅ Confirmed |
| **Notes** | Covers: source code, configuration files, database dumps, secrets, API keys, certificates, logs, and any data inaccessible to unprivileged users. In CI/CD runner context, this includes build artifacts and signing keys. |
| **Sources** | **[S4]** Microsoft Defender: *"credential and data exfiltration"* confirmed as observed post-exploitation activity. **[S8]** XM Cyber: data collection and exfiltration confirmed in active exploitation cases. **[S6]** BleepingComputer: *"could give attackers access to every application, server, and database"* — scope of collection post-root described. |

---

### TA0040 — Impact

---

#### T1496 — Resource Hijacking

| Field | Detail |
|---|---|
| **Relevance** | Cryptomining is a common low-effort post-root outcome in cloud scanning campaigns |
| **In-the-Wild** | ⚠️ Probable — consistent with automated cloud scanning pattern |
| **Notes** | Automated cloud scanning + Copy Fail LPE → root → cryptominer deployment is a known opportunistic playbook. Root access enables bypassing CPU/resource quotas that would otherwise limit miner output. |
| **Sources** | **[S8]** XM Cyber: active exploitation characterized as cloud-environment-focused automated scanning — consistent with cryptomining deployment pattern. **[S4]** Microsoft Defender: *"multi-tenant Linux hosts"* and cloud environments identified as priority targets; automated scanning pattern noted. Resource hijacking as a post-exploitation outcome is well-established in cloud LPE scenarios of this type. |

---

#### T1486 — Data Encrypted for Impact

| Field | Detail |
|---|---|
| **Relevance** | Ransomware deployment enabled by root access |
| **In-the-Wild** | ⚠️ Not yet confirmed for Copy Fail specifically — high-probability future use |
| **Notes** | Copy Fail as an LPE step in a ransomware kill chain is a natural evolution: initial foothold (phishing/RCE) → Copy Fail (root) → ransomware deployment with full filesystem access. Copy Fail is more capable than Dirty Pipe for this use case due to broader platform coverage. |
| **Sources** | **[S1]** Xint: comparison to Dirty Pipe — *"Copy Fail is more portable. One script, every distro, no offsets. Dirty Pipe needed kernel ≥ 5.8 with specific patches; Copy Fail covers the entire 2017–2026 window."* Dirty Pipe was weaponised in ransomware campaigns; Copy Fail presents the same pathway with broader reach. **[S4]** Microsoft Defender: *"Kubernetes/container clusters, CI runners/build farms, and cloud SaaS running user code"* identified as priority ransomware-risk environments. |

---

#### T1485 — Data Destruction

| Field | Detail |
|---|---|
| **Relevance** | Root enables wiping of logs, backups, and system state |
| **In-the-Wild** | ⚠️ Probable in state-sponsored / destructive attack scenarios |
| **Notes** | With root, attacker can `rm -rf /var/log/*`, overwrite backup directories, destroy database files, and remove audit trails. In critical infrastructure or hacktivist contexts, destructive impact is a real risk. |
| **Sources** | **[S5]** CISA KEV listing confirms active exploitation — CISA's prioritization of federal patching implies risk to critical infrastructure extends beyond data theft. **[S4]** Microsoft Defender: *"high impact to... availability"* cited in impact assessment; destructive use cases included in threat model. |

---

## Technique Quick-Reference Table

| ATT&CK ID | Technique | Sub-technique | Tactic | In Wild | Confidence | Key Source |
|---|---|---|---|---|---|---|
| T1078 | Valid Accounts | .003 / .004 | Initial Access | ✅ | 🔴 HIGH | S7, S4 |
| T1190 | Exploit Public-Facing Application | — | Initial Access | ⚠️ | 🟡 MEDIUM | S4, S13 |
| T1133 | External Remote Services | — | Initial Access | ✅ | 🔴 HIGH | S7, S4 |
| T1059.006 | Command and Scripting: Python | — | Execution | ✅ | 🔴 HIGH | S2, S4 |
| T1106 | Native API | — | Execution | ✅ | 🔴 HIGH | S1, S3 |
| T1611 | Escape to Host | — | Execution / Priv Esc | ✅ | 🔴 HIGH | S9, S4, S1 |
| T1543.002 | Create/Modify System Process: Systemd | — | Persistence | ✅ | 🔴 HIGH | S4, S8 |
| T1098 | Account Manipulation | — | Persistence | ⚠️ | 🟡 MEDIUM | S4, S7 |
| T1053.003 | Scheduled Task: Cron | — | Persistence | ⚠️ | 🟡 MEDIUM | S4, S8 |
| T1068 | Exploitation for Privilege Escalation | — | Privilege Escalation | ✅ | 🔴 HIGH | S1, S4, S5, S10 |
| T1211 | Exploitation for Defense Evasion | — | Defense Evasion | ✅ | 🔴 HIGH | S1, S3, S4 |
| T1070.004 | Indicator Removal: File Deletion | — | Defense Evasion | ✅ | 🔴 HIGH | S3, S4 |
| T1036 | Masquerading | — | Defense Evasion | ✅ | 🔴 HIGH | S2, S12 |
| T1027 | Obfuscated Files or Information | — | Defense Evasion | ✅ | 🔴 HIGH | S1, S2 |
| T1562.001 | Impair Defenses: Disable/Modify Tools | — | Defense Evasion | ✅ | 🔴 HIGH | S4, S8 |
| T1222.002 | File/Dir Permissions Modification: Linux | — | Defense Evasion | ⚠️ | 🟡 MEDIUM | S4, S7 |
| T1003.008 | OS Credential Dumping: /etc/shadow | — | Credential Access | ✅ | 🔴 HIGH | S4, S7 |
| T1552.004 | Unsecured Credentials: Private Keys | — | Credential Access | ✅ | 🔴 HIGH | S4, S8 |
| T1552.007 | Unsecured Credentials: Container API | — | Credential Access | ⚠️ | 🔴 HIGH | S9, S4, S1 |
| T1082 | System Information Discovery | — | Discovery | ✅ | 🔴 HIGH | S4, S1 |
| T1613 | Container and Resource Discovery | — | Discovery | ⚠️ | 🔴 HIGH | S9, S4 |
| T1083 | File and Directory Discovery | — | Discovery | ✅ | 🔴 HIGH | S1, S2, S3 |
| T1087 | Account Discovery | — | Discovery | ✅ | 🔴 HIGH | S7, S4 |
| T1057 | Process Discovery | — | Discovery | ⚠️ | 🟡 MEDIUM | S4, S3 |
| T1210 | Exploitation of Remote Services | — | Lateral Movement | ✅ | 🔴 HIGH | S4, S7 |
| T1021.004 | Remote Services: SSH | — | Lateral Movement | ✅ | 🔴 HIGH | S7, S4 |
| T1005 | Data from Local System | — | Collection | ✅ | 🔴 HIGH | S4, S8 |
| T1496 | Resource Hijacking | — | Impact | ⚠️ | 🟡 MEDIUM | S8, S4 |
| T1486 | Data Encrypted for Impact | — | Impact | ⚠️ | 🟡 MEDIUM | S1, S4 |
| T1485 | Data Destruction | — | Impact | ⚠️ | 🟡 MEDIUM | S5, S4 |

**Legend:** ✅ Confirmed in-the-wild | ⚠️ Probable / demonstrated but not confirmed in Copy Fail-specific incidents

---

## ATT&CK Navigator JSON

The following can be imported directly into [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) at `https://mitre-attack.github.io/attack-navigator/`.

```json
{
  "name": "CVE-2026-31431 Copy Fail",
  "versions": { "attack": "16", "navigator": "5.1.0", "layer": "4.5" },
  "domain": "enterprise-attack",
  "description": "MITRE ATT&CK coverage for CVE-2026-31431 (Copy Fail) — Linux kernel LPE via algif_aead page cache poisoning. Includes LPE primitive, observed ITW TTPs, and post-exploitation capabilities. Last updated 2026-05-05.",
  "filters": { "platforms": ["Linux", "Containers"] },
  "sorting": 0,
  "layout": { "layout": "side", "aggregateFunction": "average", "showID": true, "showName": true },
  "hideDisabled": false,
  "techniques": [
    { "techniqueID": "T1078.003", "tactic": "initial-access",       "color": "#ff6666", "comment": "[S7,S4] Stolen SSH/SSLVPN credentials — confirmed ITW (Huntress Apr 30; Microsoft Defender May 1)", "enabled": true, "score": 100 },
    { "techniqueID": "T1078.004", "tactic": "initial-access",       "color": "#ff9999", "comment": "[S4] Cloud account credential stuffing in automated scanning campaigns (Microsoft Defender May 1)", "enabled": true, "score": 75 },
    { "techniqueID": "T1190",     "tactic": "initial-access",       "color": "#ffcc99", "comment": "[S4,S13] Web app RCE → container shell — Microsoft Defender: 'container footholds' as observed precursor; CERT-EU: K8s/CI runners priority hardening", "enabled": true, "score": 60 },
    { "techniqueID": "T1133",     "tactic": "initial-access",       "color": "#ff6666", "comment": "[S7,S4] SSLVPN compromised credential — confirmed entry in Huntress Apr 30 intrusion; SSH access confirmed Microsoft Defender", "enabled": true, "score": 100 },
    { "techniqueID": "T1059.006", "tactic": "execution",            "color": "#ff6666", "comment": "[S2,S4] 732-byte Python PoC (stdlib only); Exploit:Python/CopyFail.A active detection signature (Microsoft Defender May 1)", "enabled": true, "score": 100 },
    { "techniqueID": "T1106",     "tactic": "execution",            "color": "#ff6666", "comment": "[S1,S3] AF_ALG socket + splice() + sendmsg/recvmsg — all standard unprivileged syscalls (Xint disclosure; Sysdig TRT Apr 30)", "enabled": true, "score": 100 },
    { "techniqueID": "T1611",     "tactic": "execution",            "color": "#ff9999", "comment": "[S9,S4,S1] K8s PoC confirmed pod-to-host <10s (Percivalll May 5); Microsoft Defender: container escape confirmed; Xint: 'Kubernetes node compromise vector'", "enabled": true, "score": 85 },
    { "techniqueID": "T1543.002", "tactic": "persistence",          "color": "#ff6666", "comment": "[S4,S8] Backdoor deployment via systemd confirmed in Microsoft Defender telemetry (May 1); XM Cyber persistence observed", "enabled": true, "score": 100 },
    { "techniqueID": "T1098",     "tactic": "persistence",          "color": "#ffcc99", "comment": "[S4,S7] Account-level persistence in Microsoft Defender post-exploitation phase; consistent with Huntress intrusion chain", "enabled": true, "score": 60 },
    { "techniqueID": "T1053.003", "tactic": "persistence",          "color": "#ffcc99", "comment": "[S4,S8] Post-root persistence mechanisms noted; consistent with cloud scanning campaign pattern (Microsoft Defender; XM Cyber)", "enabled": true, "score": 60 },
    { "techniqueID": "T1068",     "tactic": "privilege-escalation", "color": "#cc0000", "comment": "[S1,S5,S10,S4] CORE — CVE-2026-31431. Unprivileged→UID 0, deterministic (Xint/Theori Apr 29); CISA KEV May 1; NVD CVSS 7.8; Microsoft Defender active telemetry", "enabled": true, "score": 100 },
    { "techniqueID": "T1211",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "[S1,S3,S4] Page cache only — on-disk file unchanged; FIM bypass confirmed (Xint: 'on-disk file unchanged'; Sysdig TRT; Microsoft Defender)", "enabled": true, "score": 100 },
    { "techniqueID": "T1070.004", "tactic": "defense-evasion",      "color": "#ff6666", "comment": "[S3,S4] Script deletion post-root; memory modifications revert on reboot — no disk artifact (Sysdig TRT; Microsoft Defender Phase 5)", "enabled": true, "score": 100 },
    { "techniqueID": "T1036",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "[S2,S12] EICAR string reversed at compile time, flipped at runtime — documented in Theori PoC source; confirmed CloudSEK analysis", "enabled": true, "score": 100 },
    { "techniqueID": "T1027",     "tactic": "defense-evasion",      "color": "#ff6666", "comment": "[S1,S2] 732-byte script; stdlib only; no shellcode — 'no static signatures' (Xint); confirmed Theori PoC code", "enabled": true, "score": 100 },
    { "techniqueID": "T1562.001", "tactic": "defense-evasion",      "color": "#ff6666", "comment": "[S4,S8] SELinux/AppArmor/auditd/Falco disable confirmed post-root (Microsoft Defender May 1); XM Cyber active campaigns", "enabled": true, "score": 100 },
    { "techniqueID": "T1222.002", "tactic": "defense-evasion",      "color": "#ffcc99", "comment": "[S4,S7] System-state modification post-root observed; chmod/chattr consistent with Microsoft Defender post-exploitation activity", "enabled": true, "score": 60 },
    { "techniqueID": "T1003.008", "tactic": "credential-access",    "color": "#ff6666", "comment": "[S4,S7,S8] /etc/shadow credential exfiltration confirmed (Microsoft Defender May 1); Huntress post-SYSTEM credential harvesting; XM Cyber", "enabled": true, "score": 100 },
    { "techniqueID": "T1552.004", "tactic": "credential-access",    "color": "#ff6666", "comment": "[S4,S8,S6] SSH keys/K8s tokens/.aws credentials confirmed (Microsoft Defender; XM Cyber; BleepingComputer data center blast radius)", "enabled": true, "score": 100 },
    { "techniqueID": "T1552.007", "tactic": "credential-access",    "color": "#ff9999", "comment": "[S9,S4,S1] Kubelet API access after container escape — Percivalll K8s PoC; Microsoft Defender 'millions of K8s clusters'; Xint 'K8s node compromise vector'", "enabled": true, "score": 85 },
    { "techniqueID": "T1082",     "tactic": "discovery",            "color": "#ff6666", "comment": "[S4,S1,S8] uname -r Phase 1 recon confirmed (Microsoft Defender); automated kernel scanning in wild (XM Cyber; Xint PoC step 1)", "enabled": true, "score": 100 },
    { "techniqueID": "T1613",     "tactic": "discovery",            "color": "#ff9999", "comment": "[S9,S4,S1] Node-level enumeration post-escape (Percivalll K8s PoC); Microsoft Defender: 'impact radius from one container to entire node'; Xint: 'K8s node compromise vector'", "enabled": true, "score": 85 },
    { "techniqueID": "T1083",     "tactic": "discovery",            "color": "#ff6666", "comment": "[S1,S2,S3] Setuid binary enumeration — find / -perm -4000; /usr/bin/su default target (Xint; Theori PoC; Sysdig TRT)", "enabled": true, "score": 100 },
    { "techniqueID": "T1087",     "tactic": "discovery",            "color": "#ff6666", "comment": "[S7,S4] whoami/id/cat /etc/passwd confirmed in Huntress intrusion chain; Microsoft Defender Phase 1 recon", "enabled": true, "score": 100 },
    { "techniqueID": "T1057",     "tactic": "discovery",            "color": "#ffcc99", "comment": "[S4,S3] Security tool probing in Microsoft Defender Phase 1; Sysdig Falco rule implies attacker EDR awareness", "enabled": true, "score": 60 },
    { "techniqueID": "T1210",     "tactic": "lateral-movement",     "color": "#ff6666", "comment": "[S4,S7] Lateral movement confirmed (Microsoft Defender); AD recon + lateral movement prep in Huntress intrusion", "enabled": true, "score": 100 },
    { "techniqueID": "T1021.004", "tactic": "lateral-movement",     "color": "#ff6666", "comment": "[S7,S4] SSH lateral movement in Huntress intrusion; Microsoft Defender: credential rotation recommended post-compromise", "enabled": true, "score": 100 },
    { "techniqueID": "T1005",     "tactic": "collection",           "color": "#ff6666", "comment": "[S4,S8,S6] Data exfiltration confirmed (Microsoft Defender; XM Cyber); BleepingComputer: 'every application, server, and database' accessible", "enabled": true, "score": 100 },
    { "techniqueID": "T1496",     "tactic": "impact",               "color": "#ffcc99", "comment": "[S8,S4] Cloud scanning pattern consistent with cryptomining playbook (XM Cyber; Microsoft Defender automated scanning telemetry)", "enabled": true, "score": 60 },
    { "techniqueID": "T1486",     "tactic": "impact",               "color": "#ffcc99", "comment": "[S1,S4] Broader reach than Dirty Pipe — ransomware pathway (Xint comparison); Microsoft Defender: K8s/CI runners priority ransomware targets", "enabled": true, "score": 60 },
    { "techniqueID": "T1485",     "tactic": "impact",               "color": "#ffcc99", "comment": "[S5,S4] CISA KEV critical infrastructure priority; Microsoft Defender: 'high impact to availability'; destructive scenarios in scope", "enabled": true, "score": 60 }
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
    { "name": "Last Updated", "value": "2026-05-05" }
  ]
}
```

---

*Created: 2026-05-04 | Updated: 2026-05-05 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
