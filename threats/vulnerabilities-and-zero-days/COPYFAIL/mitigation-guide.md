# Copy Fail (CVE-2026-31431) — Immediate Mitigation Guide

> **Status:** CISA KEV listed; preliminary exploitation activity active. Ubuntu kmod mitigation released. Amazon Linux, RHEL, SUSE kernel patches still PENDING.
> **Apply controls below immediately. Ubuntu users: also run the kmod upgrade below.**
> Last updated: 2026-05-04

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

## ✅ Step 1 — Disable the algif_aead Module (Do This Now, All Other Distros)

The fastest, most complete mitigation available. Completely eliminates the attack surface.

```bash
# Block module from loading on reboot
echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf

# Remove from running kernel immediately
rmmod algif_aead 2>/dev/null || true

# Verify it's gone
lsmod | grep algif_aead   # Should return nothing
```

**Survives reboots.** The `modprobe.d` entry prevents the module from loading again after restart.

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
| Amazon Linux | https://explore.alas.aws.amazon.com/CVE-2026-31431.html | ⚠️ KERNEL PENDING |
| RHEL | https://access.redhat.com/security/cve/CVE-2026-31431 | ⚠️ KERNEL PENDING |
| Ubuntu 14.04–25.10 | https://ubuntu.com/security/CVE-2026-31431 | ⚠️ KERNEL PENDING (kmod mitigation ✅ available) |
| Ubuntu 26.04 Resolute | N/A | ✅ NOT AFFECTED |
| SUSE | https://www.suse.com/security/cve/CVE-2026-31431 | ⚠️ KERNEL PENDING |
| AlmaLinux | https://errata.almalinux.org | ✅ PATCHED |
| Arch / rolling | Kernel 6.19.12+ available | ✅ PATCHED |

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

*Guide created: 2026-05-01 | Updated: 2026-05-04 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
