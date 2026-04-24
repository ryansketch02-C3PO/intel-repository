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
---

# Follow-Up Hardening Session
**Date:** 2026-04-24
**Host:** [HOSTNAME] (Raspberry Pi, Debian GNU/Linux 12 Bookworm, arm64)

---

## Pre-Session Assessment

### Audit Results (openclaw security audit --deep)
- 🔴 2 critical issues
- ⚠️ 4 warnings
- ℹ️ 1 info

### Open Ports
| Port | Service | Exposure |
|------|---------|----------|
| 631 | CUPS (printing) | Loopback only ✅ |
| 18789/tcp | OpenClaw Gateway | Loopback only ✅ |
| 22/tcp | SSH | All interfaces ⚠️ |
| 3350 | Cendio Thinlinc | Loopback only ✅ |

### Key Findings
- **No firewall active** — ufw installed but inactive, nft/firewalld not present
- **Elevated exec wildcard** — `*` set for both webchat and Discord channels (critical)
- **OpenClaw out of date** — running 2026.3.13, latest 2026.4.22

---

## Changes Made

### 1. Enabled ufw Firewall
ufw was already installed. Activated with:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw --force enable
```

### 2. Locked Elevated Exec Allowlist
Removed `*` wildcard from Discord elevated exec allowlist.
Locked to owner Discord user ID only via `config.patch`.
Webchat left as `*` (loopback-only bind — local access only, low risk).

### 3. Updated OpenClaw
```
Before: 2026.3.13
After:  2026.4.22
```
Gateway came back up cleanly. Webchat confirmed unaffected (HTTP 200).

### 4. Pi Performance Optimisations
Added to systemd service unit (`openclaw-gateway.service`):
```
Environment=NODE_COMPILE_CACHE=/var/tmp/openclaw-compile-cache
Environment=OPENCLAW_NO_RESPAWN=1
```

### 5. Maintenance
- Archived 28 orphan transcript files (soft-deleted with timestamps)

---

## Post-Session Firewall Status
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)

To             Action      From
--             ------      ----
22/tcp         ALLOW IN    Anywhere
22/tcp (v6)    ALLOW IN    Anywhere (v6)
```

## Services Confirmed Unaffected
- ✅ OpenClaw / Discord bot ([BOT-NAME]) — online
- ✅ OpenClaw webchat — HTTP 200, loopback
- ✅ Pi Connect — outbound relay, unaffected
- ✅ [TTS-SERVICE] — unaffected

---

## Remaining Recommendations
1. **SSH hardening** — key-only auth, disable password login
2. **Automatic security updates** — enable unattended-upgrades
3. **Disk encryption** — status still unknown
4. **Discord channel permissions** — two channel IDs the bot can't access ([DISCORD-ID-REDACTED], [DISCORD-ID-REDACTED])
5. **Memory search** — no embedding provider configured (semantic recall disabled)
### 6. SSH Hardening
Disabled password authentication — key-only login enforced:
```bash
PasswordAuthentication no
sudo systemctl restart ssh
```
Key type: ed25519, generated on Pi, copied to client via scp.
Fingerprint: `[FINGERPRINT-REDACTED]`

### 7. Automatic Security Updates
Installed and enabled unattended-upgrades:
```bash
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure -f noninteractive unattended-upgrades
```
Config: `APT::Periodic::Update-Package-Lists "1"; APT::Periodic::Unattended-Upgrade "1";`

---

## ✅ All Recommended Items Resolved (2026-04-24)
- Firewall (ufw): active, default deny
- Elevated exec wildcard: removed
- OpenClaw: updated to 2026.4.22
- SSH: key-only auth enforced
- Automatic security updates: enabled
