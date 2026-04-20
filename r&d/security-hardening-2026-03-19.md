# Security Hardening Log
**Date:** 2026-03-19  
**Host:** [HOSTNAME] (Raspberry Pi, Debian GNU/Linux 12 Bookworm, arm64)

---

## Pre-Hardening Assessment

### Audit Results (openclaw security audit --deep)
- ✅ 0 critical issues
- ⚠️ 4 warnings
- ℹ️ 1 info

### Open Ports (pre-firewall)
| Port | Service | Exposure |
|------|---------|----------|
| 22/tcp | SSH | Public internet ⚠️ |
| 3389/tcp | RDP | Public internet 🔴 |
| 631 | CUPS (printing) | Loopback only ✅ |
| 3350 | Cendio Thinlinc | Loopback only ✅ |
| 18789/tcp | OpenClaw Gateway | Loopback only ✅ |
| 18791/tcp | OpenClaw internal | Loopback only ✅ |
| 18792/tcp | OpenClaw internal | Loopback only ✅ |

### Key Findings
- **No firewall installed** — ufw/firewalld not present
- **RDP (3389) exposed publicly** — high-risk, commonly targeted by attackers
- **SSH (22) exposed publicly** — being probed by bots constantly
- **OpenClaw dashboard** — loopback-only, NOT directly internet-exposed ✅
- **OpenClaw version** — up to date (2026.3.13) ✅
- **Discord slash commands** — no user allowlist configured (commands rejected for everyone)
- **Multi-user trust warning** — Discord groupPolicy=allowlist with group targets; multiple users could potentially interact with the assistant

---

## Changes Made

### 1. Installed ufw Firewall
```bash
sudo apt-get install -y ufw
```
Installed: `ufw 0.36.2-1`, `iptables 1.8.9-2`, `libip6tc2 1.8.9-2`

### 2. Set Default Policies
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
- All inbound connections blocked by default
- All outbound connections allowed (Discord, Perplexity, web access unaffected)

### 3. Allowed SSH
```bash
sudo ufw allow 22/tcp
```
SSH access preserved for remote management.

### 4. Enabled Firewall
```bash
sudo ufw --force enable
```
Firewall active and enabled on system startup.

---

## Post-Hardening Status

```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To          Action      From
--          ------      ----
22/tcp      ALLOW IN    Anywhere
22/tcp (v6) ALLOW IN    Anywhere (v6)
```

### Port Status After Hardening
| Port | Service | Status |
|------|---------|--------|
| 22/tcp | SSH | Open (intentional) ✅ |
| 3389/tcp | RDP | Blocked by firewall 🔒 |
| All others | — | Blocked by default 🔒 |

### Services Confirmed Unaffected
- ✅ OpenClaw / Discord bot — outbound only, unaffected
- ✅ Perplexity API — outbound only, unaffected
- ✅ Pi Connect — outbound relay, unaffected
- ✅ OpenClaw dashboard (webchat) — loopback only, unaffected

---

## Remaining Recommendations

These were identified but not yet actioned:

1. **SSH hardening** — disable password auth, key-only login, consider changing default port
2. **Discord user allowlist** — add Ryan's Discord user ID so slash commands work
3. **Automatic security updates** — enable unattended-upgrades for OS patches
4. **Disk encryption** — status unknown; recommended if Pi is physically accessible

---

## Notes
- Pi Connect remote access confirmed working after firewall (uses outbound relay)
- RDP port 3389 was open but it's unclear if it was actively used; now blocked
- OpenClaw gateway was already secure (loopback-only bind) before this hardening pass
