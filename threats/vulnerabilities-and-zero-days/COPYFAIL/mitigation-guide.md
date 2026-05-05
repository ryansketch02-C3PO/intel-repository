# Copy Fail (CVE-2026-31431) — Immediate Mitigation Guide

> **Status:** CISA KEV listed; active exploitation confirmed. Ubuntu kmod mitigation released. Amazon Linux, RHEL, SUSE kernel patches still PENDING.
> ⚠️ **RHEL/AlmaLinux/Rocky/CloudLinux users: `rmmod` and `modprobe.d` do NOT work — see Step 1a.**
> Last updated: 2026-05-05

---

## ✅ Step 0 — Ubuntu-Specific: Apply kmod Mitigation Package

Ubuntu has released an updated `kmod` package that automatically applies the module disable via `modprobe.d`. **Ubuntu 26.04 LTS Resolute is not affected** and requires no action.

```bash
# For Ubuntu 14.04–25.10 (all affected releases)
sudo apt update && sudo apt install --only-upgrade kmod

# Reboot or manually unload to apply immediately:
sudo rmmod algif_aead 2>/dev/null || true

# Verify
grep -qE '^algif_aead ' /proc/modules && echo "Module STILL loaded" || echo "Module NOT loaded — mitigated"
```

This is the Canonical-supported mitigation path for Ubuntu. The kernel patch itself is still in progress — watch for updates via `ubuntu.com/security/CVE-2026-31431`.

---

## ✅ Step 1 — Disable the algif_aead Module (Debian, SUSE, Amazon Linux, Arch)

The fastest, most complete mitigation where applicable. Completely eliminates the attack surface.

```bash
# Block module from loading on reboot
echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf

# Remove from running kernel immediately
rmmod algif_aead 2>/dev/null || true

# Verify it's gone
lsmod | grep algif_aead   # Should return nothing
```

**Survives reboots.** The `modprobe.d` entry prevents the module from loading again after restart.

> ⚠️ **This step does NOT apply to RHEL-family systems.** See Step 1a below.

---

## ⚠️ Step 1a — RHEL / AlmaLinux / Rocky Linux / CloudLinux: Different Approach Required

**`rmmod` and `modprobe.d` blacklisting have no effect on RHEL-family systems** because `algif_aead` is compiled directly into the kernel (`CONFIG_CRYPTO_USER_API_AEAD=y`), not built as a loadable module. There is no module to remove or block.

### First: confirm whether you're affected
```bash
# Check if algif_aead is a loadable module or built-in
lsmod | grep algif_aead         # Returns nothing if built-in OR if not loaded
grep algif_aead /proc/crypto    # Returns entry if built-in AND available
grep -r algif_aead /lib/modules/$(uname -r)/  # Returns nothing if compiled in
```
If `/proc/crypto` shows `algif_aead` but `lsmod` shows nothing — it's compiled in and `rmmod` will not help.

### Mitigations available on RHEL-family (without kernel patch)

**Option A — SELinux Enforcement (Recommended — Already Active on Most RHEL Systems)**

SELinux in enforcing mode is confirmed to block the Copy Fail exploit on RHEL. If you have SELinux enabled (the default on RHEL), verify it is in enforcing mode:

```bash
getenforce   # Should return "Enforcing"

# If it returns "Permissive" or "Disabled", switch to enforcing:
setenforce 1

# Make permanent:
sed -i 's/SELINUX=permissive/SELINUX=enforcing/' /etc/selinux/config
sed -i 's/SELINUX=disabled/SELINUX=enforcing/' /etc/selinux/config
```

> ⚠️ Do not disable SELinux as a workaround for other issues on RHEL — it is your primary Copy Fail mitigation until the kernel patch arrives.

**Option B — Seccomp Block (Containers / Kubernetes)**

Block `AF_ALG` socket creation (domain=38) at the seccomp layer for all container workloads (see Step 2 below). This is effective regardless of whether `algif_aead` is a module or compiled in.

**Option C — AppArmor (if deployed)**

Restrict `AF_ALG` socket creation to known disk-encryption processes only via AppArmor profile.

**Option D — KernelCare Livepatch (Commercial)**

KernelCare can apply the upstream fix in memory without a reboot — no module interaction required. Useful for RHEL systems that cannot afford a maintenance window.

### The only complete fix for RHEL
Apply the Red Hat kernel errata when released. Monitor: https://access.redhat.com/security/cve/CVE-2026-31431

---

### What it breaks

Only breaks applications that use userspace AEAD crypto via `AF_ALG` sockets:
- `cryptsetup` / LUKS disk encryption tooling
- `kcapi-enc`, `kcapi-dgst`, `kcapi-rng`, `kcapi-sym`
- Some HSM and hardware crypto acceleration integrations

**For most enterprise servers, CI/CD runners, and container hosts — impact is zero.**

### Check before disabling (confirm nothing is using it)

```bash
# Check if module is loaded
lsmod | grep algif_aead

# Check for active AF_ALG AEAD sockets
ss -f alg 2>/dev/null | grep aead

# Search for processes using algif_aead
lsof 2>/dev/null | grep algif
```

If all return nothing → safe to disable with zero impact.

---

## Step 2 — Containers / Kubernetes (Add Seccomp Block)

The module disable works at the host kernel level. For container workloads, add defense-in-depth by blocking `AF_ALG` socket creation (domain=38) at the seccomp layer:

### Docker run (ad-hoc)
```bash
docker run --security-opt seccomp=/path/to/no-af-alg.json ...
```

### Seccomp profile snippet (`no-af-alg.json`)
```json
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "names": ["socket"],
      "action": "SCMP_ACT_ERRNO",
      "args": [
        {
          "index": 0,
          "value": 38,
          "op": "SCMP_CMP_EQ"
        }
      ]
    }
  ]
}
```

### Kubernetes — Pod Security / RuntimeDefault verification
```bash
# Verify container runtime default seccomp profile is applied
kubectl get pod <pod-name> -o jsonpath='{.spec.securityContext.seccompProfile}'

# Check node-level runtime default
docker info | grep "Security Options"
containerd config dump | grep seccomp
```

Most modern Kubernetes clusters with `RuntimeDefault` seccomp already block `AF_ALG` — verify rather than assume.

---

## Step 3 — Deploy Runtime Detection

### Falco Rule (Sysdig TRT, April 30, 2026)

Add to your Falco ruleset to alert on exploit attempt **before** root is achieved:

```yaml
- list: known_af_alg_binaries
  items: [cryptsetup, "systemd-cryptse", "systemd-cryptsetup", veritysetup,
          integritysetup, "cryptsetup-resh", kcapi-enc, kcapi-dgst, kcapi-rng, kcapi-sym]

- macro: successful_af_alg_socket
  condition: >
    evt.type = socket and evt.rawres >= 0 and
    (evt.arg.domain contains AF_ALG or evt.rawarg.domain = 38)

- macro: successful_af_alg_seqpacket_socket
  condition: >
    successful_af_alg_socket and
    (evt.arg.type = 5 or evt.arg.type = 2053 or
     evt.arg.type = 524293 or evt.arg.type = 526341)

- rule: Unexpected Process Using Kernel AEAD Crypto Socket
  desc: >
    CVE-2026-31431 Copy Fail — mandatory first step of exploit chain.
    AF_ALG SEQPACKET socket creation from outside known disk-encryption toolchain.
  condition: >
    successful_af_alg_seqpacket_socket and
    not proc.name in (known_af_alg_binaries)
  output: >
    ALERT CVE-2026-31431: AF_ALG AEAD socket from unexpected process
    (proc=%proc.name pid=%proc.pid user=%user.name
     cmdline=%proc.cmdline container=%container.name)
  priority: CRITICAL
  tags: [host, container, kernel, CVE-2026-31431, MITRE_T1068]
```

### Linux Kernel Audit Rule

```bash
# Audit all AF_ALG socket creation (logs to /var/log/audit/audit.log)
auditctl -a always,exit -F arch=b64 -S socket -F a0=38 -k af_alg_copyfail

# Make persistent across reboots
echo "-a always,exit -F arch=b64 -S socket -F a0=38 -k af_alg_copyfail" \
  >> /etc/audit/rules.d/copyfail.rules
augenrules --load
```

---

## Step 4 — Patch When Available

Monitor your distro's security advisory channel and apply the kernel update as soon as it lands:

| Distro | Advisory / Watch Link | Status |
|---|---|---|
| Ubuntu 22.04 / 24.04 / 24.10 | https://ubuntu.com/security/CVE-2026-31431 | ✅ PATCHED — `apt update && apt install linux-image-generic` + reboot |
| Ubuntu 26.04 Resolute | N/A | ✅ NOT AFFECTED |
| Ubuntu 14.04–20.04 LTS | https://ubuntu.com/security/CVE-2026-31431 | ⚠️ VERIFY — kmod mitigation available; check USN for kernel backport |
| RHEL 9 / 10 | https://access.redhat.com/security/cve/CVE-2026-31431 | ✅ PATCHED — `dnf update kernel` + reboot (`kernel-7.0.0-553.42.1.el9`) |
| AlmaLinux 9 / 10 | https://errata.almalinux.org | ✅ PATCHED — `dnf update kernel` + reboot |
| Rocky Linux 9 / 10 | https://errata.rockylinux.org | ✅ PATCHED — `dnf update kernel` + reboot |
| Amazon Linux 2023 | https://explore.alas.aws.amazon.com/CVE-2026-31431.html | ✅ PATCHED — `dnf update kernel` + reboot |
| Amazon Linux 2 | https://explore.alas.aws.amazon.com/CVE-2026-31431.html | ⚠️ VERIFY — check ALAS advisory |
| SUSE SLES 15 SP5/SP6 | https://www.suse.com/security/cve/CVE-2026-31431 | ✅ PATCHED — `zypper update kernel-default` + reboot (6.18.22) |
| Debian 12 / 13 | https://security-tracker.debian.org/tracker/CVE-2026-31431 | ✅ PATCHED — `apt update && apt install linux-image-amd64` + reboot |
| Arch / rolling | Kernel 7.0 available | ✅ PATCHED |

### After patching — remove the modprobe workaround (optional)

If your application stack doesn't need `algif_aead`, you can leave the block in place permanently. If you do need to re-enable:

```bash
rm /etc/modprobe.d/disable-algif.conf
modprobe algif_aead
```

---

## Priority Order Summary

| Priority | Action | Time to complete |
|---|---|---|
| 🔴 **Do now (Ubuntu)** | `sudo apt install --only-upgrade kmod` + rmmod (Step 0) | < 2 minutes |
| 🔴 **Do now (all others)** | Disable `algif_aead` module (Step 1) | < 60 seconds |
| 🔴 **Do now** | Verify seccomp on all container workloads (Step 2) | 5–10 minutes |
| 🔴 **Do soon** | Deploy Falco/audit detection rule (Step 3) | 15–30 minutes |
| ⏳ **Monitor** | Apply distro kernel patch when released (Step 4) | When available |

---

## Step 2a — Kubernetes: Cluster-Wide Mitigation DaemonSet

For teams managing large clusters who cannot patch node kernels individually, **Deckhouse** has published an open-source DaemonSet manifest (`d8-copy-fail-mitigation`) that applies the `algif_aead` module disable across all nodes automatically:

```bash
# Apply the Deckhouse DaemonSet to your cluster
kubectl apply -f https://raw.githubusercontent.com/deckhouse/deckhouse/main/modules/040-node-manager/d8-copy-fail-mitigation.yaml

# Verify it's running on all nodes
kubectl get daemonset d8-copy-fail-mitigation -n kube-system
kubectl get pods -n kube-system -l app=d8-copy-fail-mitigation
```

> ⚠️ The DaemonSet approach has the same RHEL caveat: it runs `rmmod algif_aead` on each node, which has no effect on RHEL-family nodes with a compiled-in module. Pair with seccomp enforcement (Step 2) on RHEL Kubernetes nodes.

---

## 🟢 Patch Status Summary (May 5, 2026)

Most major enterprise distros now have kernel patches available. **Patch and reboot** is the priority action for all systems.

| Distro | Patched Kernel | Command |
|---|---|---|
| Ubuntu 22.04 / 24.04 / 24.10 | 6.19.12 | `apt update && apt install linux-image-generic` |
| Debian 12 / 13 | 6.19.12 | `apt update && apt install linux-image-amd64` |
| RHEL 9 / 10 | kernel-7.0.0-553.42.1.el9 | `dnf update kernel` |
| AlmaLinux 9 / 10 | kernel-7.0.0-553.42.1.el9 | `dnf update kernel` |
| Rocky Linux 9 / 10 | kernel-7.0.0-553.42.1.el9 | `dnf update kernel` |
| Amazon Linux 2023 | 6.19.12 | `dnf update kernel` |
| SUSE SLES 15 SP5/SP6 | 6.18.22 | `zypper update kernel-default` |
| Arch Linux | 7.0 | `pacman -Syu` |

> **All patch commands require a system reboot to activate the new kernel.**
> After rebooting: `uname -r` should show the patched version. Compare against the table above.

---

*Guide created: 2026-05-01 | Updated: 2026-05-05 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
