# Executive Summary — CVE-2026-31431 "Copy Fail"

A local privilege escalation in the Linux kernel affecting all distributions built since 2017. A 732-byte public exploit works out-of-the-box on Ubuntu, Amazon Linux, RHEL, and SUSE. **CISA has added CVE-2026-31431 to the Known Exploited Vulnerabilities (KEV) catalog.** Microsoft Defender is observing preliminary threat-actor testing activity and expects exploitation to increase imminently. This is no longer a "watch and wait" situation.

The flaw requires an existing local foothold — it is not directly remotely exploitable. However, any web app compromise, phishing-delivered shell, CI/CD runner code execution, or container breakout immediately becomes a reliable root escalation. Container escape across Kubernetes node boundaries is also possible.

**Impact: HIGH.** **Kernel patches are now available for most major enterprise distros as of May 5.** Patch and reboot immediately. On RHEL/AlmaLinux/Rocky: `dnf update kernel`. On Ubuntu/Debian: `apt update && apt install linux-image-generic`. On Amazon Linux 2023: `dnf update kernel`. Apply the `algif_aead` module disable as an interim measure on any system awaiting a maintenance window.

## Priority Actions

| Priority | Action |
|---|---|
| 🔴 **Do now** | **Patch + reboot**: `dnf update kernel` (RHEL/AlmaLinux/Rocky/Amazon Linux 2023) OR `apt install linux-image-generic` (Ubuntu/Debian) OR `zypper update kernel-default` (SUSE) |
| 🔴 **If can’t reboot now** | Disable `algif_aead` module: `echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf && rmmod algif_aead` (not effective on RHEL — use SELinux enforcing: `setenforce 1`) |
| 🔴 **RHEL users** | Confirm SELinux in enforcing mode: `getenforce` should return `Enforcing` — this is your interim mitigation |
| 🔴 **Now** | Block `AF_ALG` socket creation (domain=38) via seccomp on all container workloads |
| 🔴 **Now** | Block DNS for `copy.fail` at your resolver — exploit variants fetch from this domain |
| 🔴 **Soon** | Deploy Falco/audit detection rules for SEQPACKET AF_ALG socket creation |
| ⏳ **Verify** | Amazon Linux 2 — patch may lag AL2023; check ALAS advisory |

See `profile.md` for full technical detail and `mitigation-guide.md` for step-by-step remediation.

---

*Created: 2026-05-01 | Updated: 2026-05-05 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
