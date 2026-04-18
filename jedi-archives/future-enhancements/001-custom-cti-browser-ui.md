# 001 — Custom CTI Browser UI

**Status:** 💡 Idea — Future Enhancement
**Priority:** Low
**Suggested by:** Ryan, 2026-04-08

---

## Concept

A purpose-built, browser-based dashboard for the C3PO Intel CTI workflow. Rather than interacting through webchat or Discord, this would be a dedicated interface designed around our specific needs — threat actor lookups, brief history, IOC search, Scoundrel profiles, and structured analysis sessions.

---

## Why

OpenClaw's webchat is general-purpose. A custom UI could be tailored specifically to how we work:
- Quick access to Scoundrel profiles and the threat actor repo
- IOC search and cross-reference
- Brief history / archive view
- Structured analysis session launcher (Jedi Archives integration)
- Visual threat actor relationship mapping (connection webs)
- Real-time intel feed display

---

## Enabling Technology

**[claude-socket](https://github.com/cunicopia-dev/claude-socket)** — A WebSocket bridge that connects any browser-based UI to a running Claude Code session via MCP.

- Runs as a Claude Code MCP plugin
- Exposes Claude Code's channel system over WebSocket
- Supports real-time bidirectional communication, session multiplexing, permission relay, message history
- Zero-dependency browser client library (`ClaudeSocket` class)
- Permission approval from UI (replaces terminal `/approve` flow)

### How It Would Work
```
Browser UI (custom CTI dashboard)
    ↕ WebSocket
claude-socket (MCP plugin)
    ↕ MCP stdio  
Claude Code (running C3PO agent)
```

### Installation (when ready)
```bash
claude plugin marketplace add cunicopia-dev/claude-socket
claude plugin install claude-socket
claude --channels plugin:claude-socket@claude-socket
```

### Key Config
| Variable | Default | Description |
|---|---|---|
| `CLAUDE_SOCKET_PORT` | 3100 | WebSocket server port |
| `CLAUDE_SOCKET_HOST` | 127.0.0.1 | Bind address |

---

## What to Build

### Phase 1 — Basic Chat UI
- Connect claude-socket to Claude Code
- Simple browser UI that replaces webchat for CTI sessions
- Permission approval from browser (no more terminal `/approve`)

### Phase 2 — CTI Dashboard
- Threat actor directory (pulls from threat-actor-repository)
- IOC search and lookup
- Brief archive viewer
- Scoundrel profile cards

### Phase 3 — Advanced Features
- Visual connection web / relationship graph (D3.js or similar)
- Structured analysis session launcher (Jedi Archives integration)
- Real-time intel feed

---

## Prerequisites

- Claude Code installed and configured alongside OpenClaw
- Bun runtime (claude-socket dependency)
- Decision on hosting: local only vs. accessible remotely

---

## Notes

- claude-socket solves a real gap for Claude Code users; OpenClaw already covers most of this for our current workflow
- The main value add would be a **purpose-built CTI interface** — not just another chat window
- Could run alongside OpenClaw rather than replacing it
- Worth revisiting when the threat actor repo and intel workflow are more mature

---

## References

- claude-socket repo: https://github.com/cunicopia-dev/claude-socket
- Discussed: 2026-04-08, Ryan suggested as future enhancement
- C3PO assessment: Good fit for a custom CTI dashboard; not needed for current workflow

---

*Added to Jedi Archives: 2026-04-08*
