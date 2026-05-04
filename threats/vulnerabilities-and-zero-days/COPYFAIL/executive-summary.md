# Executive Summary — CVE-2026-31431 "Copy Fail"

A local privilege escalation in the Linux kernel affecting all distributions built since 2017. A 732-byte public exploit works out-of-the-box on Ubuntu, Amazon Linux, RHEL, and SUSE. **CISA has added CVE-2026-31431 to the Known Exploited Vulnerabilities (KEV) catalog.** Microsoft Defender is observing preliminary threat-actor testing activity and expects exploitation to increase imminently. This is no longer a "watch and wait" situation.

The flaw requires an existing local foothold — it is not directly remotely exploitable. However, any web app compromise, phishing-delivered shell, CI/CD runner code execution, or container breakout immediately becomes a reliable root escalation. Container escape across Kubernetes node boundaries is also possible.

**Impact: HIGH.** Apply the `algif_aead` module disable immediately on all unpatched systems. Ubuntu kmod mitigation package is available now; vendor kernel patches for RHEL, Amazon Linux, SUSE, and Debian are pending.

## Priority Actions

| Priority | Action |
|---|---|
| 🔴 **Now** | Disable `algif_aead` module: `echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf && rmmod algif_aead` |
| 🔴 **Now** | Ubuntu users: `sudo apt install --only-upgrade kmod` (releases kmod mitigation) |
| 🔴 **Now** | Block `AF_ALG` socket creation (domain=38) via seccomp on all container workloads |
| 🔴 **Soon** | Deploy Falco/audit detection rules for SEQPACKET AF_ALG socket creation |
| ⏳ **Monitor** | Vendor kernel patches: RHEL, Amazon Linux, SUSE, Debian — apply immediately when released |

See `profile.md` for full technical detail and `mitigation-guide.md` for step-by-step remediation.

---

*Created: 2026-05-01 | Updated: 2026-05-04 | Author: C3PO | CVE: CVE-2026-31431 | TLP: WHITE*
