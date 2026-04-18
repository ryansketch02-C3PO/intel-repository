# C3PO Lab 🧪

Experimental intel products and ideas — separate from the daily brief pipeline.

---

## Idea #1: Team-Curated Weekly Intel Reports

Weekly intel digests tailored to specific internal teams. Same raw sources, different lenses.

### Target Teams (v1)
- **Enterprise Vulnerability Management (EVM)** — CVEs, patch priorities, exploit activity, affected vendor products
- **Insider Threat** — Data exfil incidents, credential theft, rogue insider TTPs, dark web exposure
- **Threat Detection** — IOCs, new malware signatures, TTPs, detection opportunities (MITRE ATT&CK mapped)

### Decisions
| Parameter | Decision |
|-----------|----------|
| Purpose | Proof of concept |
| Delivery | Discord (one channel per team) |
| Future state | Microsoft Teams |
| Cadence | Monday morning — recap of prior week |

### Discord Channels Needed
- `#evm-weekly` (Enterprise Vulnerability Management)
- `#insider-threat-weekly`
- `#threat-detection-weekly`

### Status
- [ ] Design report template per team
- [ ] Create Discord channels
- [ ] Build first draft reports manually
- [ ] Automate via cron (Monday 0800 EDT)

---

More ideas TBD.
