# Project Glasswing: What Mythos Showed Us
**Source:** [Cloudflare Security Blog](https://blog.cloudflare.com/cyber-frontier-models/)
**Author:** Grant Bourzikas (CSO, Cloudflare)
**Published:** May 18, 2026
**Summarized:** May 19, 2026

---

## Overview

Cloudflare was invited by Anthropic to participate in **Project Glasswing** — a controlled program giving select organizations access to **Mythos Preview**, Anthropic's security-specialized frontier model. Cloudflare pointed it at 50+ of their own live repositories across their runtime, edge data path, protocol stack, and control plane. The stated goal: see what it finds, and — critically — see what attackers will be able to do with models like this.

---

## What Makes Mythos Preview Different

Two capabilities set it apart from general-purpose frontier models:

- **Exploit chain construction** — Real attacks chain multiple primitives together. Mythos can take a use-after-free, turn it into an arbitrary read/write, hijack control flow, build a ROP chain, and reason through the whole sequence. Previous models would find the bug and *stop*. Mythos finishes the chain, and its reasoning looks like a senior researcher, not an automated scanner.
- **Proof generation** — It doesn't just find bugs, it proves exploitability. It writes PoC code, compiles it, runs it in a scratch environment, reads the failure if it doesn't work, adjusts its hypothesis, and tries again autonomously. A suspected flaw with no PoC is speculation. Mythos closes that gap on its own.

**The key shift:** Other frontier models found many of the same underlying bugs — but couldn't stitch them together. Mythos takes low-severity bugs that would rot in a backlog and chains them into a single higher-severity exploit.

---

## The Refusal Problem

The Glasswing version of Mythos had reduced safeguards vs. the generally available model. Despite this, it had **inconsistent emergent guardrails** — the same task framed differently could produce opposite outcomes. It refused to write a PoC after finding serious memory bugs, then agreed to the same request when worded differently.

Cloudflare's conclusion: organic model refusals are real but **not reliable enough to serve as a safety boundary**. Any generally-available cyber frontier model will need additional hardening layered on top of emergent behavior.

---

## The Signal-to-Noise Problem

AI vuln scanners have made triage *harder*, not easier. Two main noise drivers:

- **Language** — C/C++ produces far more false positives than memory-safe languages like Rust. The model hallucinates bug classes that Rust eliminates at compile time.
- **Model bias** — Models will find bugs whether they exist or not. Findings are hedged ("possibly," "potentially," "could in theory") and speculative findings vastly outnumber solid ones. In a triage queue, every speculative finding costs human attention and tokens to dismiss — and it compounds at scale.

Mythos is better here: findings come with PoCs, clearer reproduction steps, and fewer hedges. But Cloudflare still runs multiple post-validation stages to manage noise.

---

## Why "Just Point It At a Repo" Doesn't Work

The naive approach — throw a generic coding agent at a codebase and ask it to find bugs — produces findings, but not useful coverage. Two reasons:

- **Context** — Coding agents hold one hypothesis at a time. Vuln research is narrow and highly parallel — you investigate one specific thing deeply, then repeat it thousands of times across the codebase. A single agent session covers maybe 0.1% of a large repo before context fills and compaction kicks in, potentially discarding earlier findings.
- **Throughput** — Single-stream agents can't fan out across the surface area that real security research requires.

---

## The Harness: What Actually Works

The solution is to build a **multi-agent harness** around the model rather than using it as a chat interface. Cloudflare's 8-stage pipeline:

| Stage | What It Does | Why It Matters |
|---|---|---|
| **Recon** | Maps the repo — trust boundaries, entry points, attack surface. Generates task queue for downstream agents. | You have to know what you're hunting before you hunt it. |
| **Hunt** | ~50 concurrent hunter agents, each scoped to one attack class + one subsystem. Each has a scratch environment to compile and run PoCs. | This is where most of the work happens. Many narrow tasks in parallel, not one exhaustive agent. |
| **Validate** | Independent adversarial agent re-reads the code and tries to *disprove* each finding. Different prompt, different model, no ability to generate new findings. | Catches a meaningful fraction of the noise the hunter wouldn't catch reviewing its own work. |
| **Gapfill** | Hunters flag areas they touched but didn't fully cover — those get re-queued for another pass. | Prevents the model from drifting toward attack classes it's already had success with. |
| **Dedupe** | Findings with the same root cause collapse to one record. | Variant analysis is a feature, not a way to inflate the queue with duplicates. |
| **Trace** | For shared library findings, fans out to check whether attacker-controlled input can actually reach the bug from outside the system. | Turns "there's a flaw" into "there's a reachable vulnerability." This is the stage that matters most. |
| **Feedback** | Reachable traces become new hunt tasks in consumer repos. | Closes the loop. The pipeline gets better as it runs. |
| **Report** | Structured output against a predefined schema, submitted to an ingest API. | Queryable data, not prose. |

**Key insight:** Two agents in deliberate disagreement (hunter + adversarial validator) is dramatically more effective than asking one agent to be careful.

---

## Implications for Security Teams

- **Defenders:** The constraint isn't the model anymore — it's the harness. Teams that build the right multi-agent architecture will get qualitatively different results from those using a chat interface.
- **Attackers:** The same capabilities are available to them. Low-severity bugs that defenders have deprioritized for years can now be chained into working exploits with AI assistance. The backlog risk is real.
- **Dual-use tension:** Cloudflare is explicitly framing this as "we need to understand what attackers can do." The same model capability that finds and chains exploits defensively can be weaponized offensively — and Mythos-class models represent a step-change in what's now accessible without deep exploit development expertise.

---

## Key Takeaways

1. Mythos Preview is a qualitative shift — not just a better scanner, but a tool that autonomously closes the gap from "found a bug" to "proven working exploit chain."
2. Generic coding agents pointed at repos don't work for security research — you need a purpose-built parallel multi-agent harness.
3. Model refusals are not a reliable safety mechanism — they're inconsistent across semantically equivalent prompts.
4. Memory-safe languages (Rust) materially reduce false-positive noise; C/C++ codebases are significantly harder to triage.
5. The attacker/defender asymmetry implication: low-severity bugs that defenders ignore are no longer safely ignorable.

---

*Tags: AI, LLM, vulnerability research, offensive security, Anthropic, Mythos, Project Glasswing, Cloudflare, multi-agent, exploit chains*
