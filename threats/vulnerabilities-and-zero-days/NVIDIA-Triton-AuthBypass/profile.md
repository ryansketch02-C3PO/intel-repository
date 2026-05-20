# NVIDIA Triton Inference Server — CVE-2026-24207 Authentication Bypass + AI Infrastructure CVE Cluster

## Identity

| Field | Details |
|---|---|
| **Lead CVE** | CVE-2026-24207 (CVSS Critical — authentication bypass → RCE/EoP/DoS) |
| **Additional CVEs** | CVE-2026-24206 (auth bypass, high) · CVE-2026-24213/24214 (DALI backend OOB/overflow) · CVE-2026-24209/24208 (path traversal) · CVE-2026-24215 (resource exhaustion) · CVE-2026-24163/24142/24160 (TRT-LLM deserialization/RCE) · CVE-2025-33255 (TRT-LLM MPI server deserialization) |
| **Vendor Advisory** | NVIDIA Security Bulletin — May 18–20, 2026 |
| **Type** | Authentication Bypass + Code Execution / Privilege Escalation / Denial of Service |
| **Affected Products** | NVIDIA Triton Inference Server (all affected versions) · NVIDIA TensorRT-LLM (TRT-LLM) |
| **Patch Status** | ⚠️ UPDATE AVAILABLE — NVIDIA security bulletin published; update to latest Triton and TRT-LLM releases immediately |
| **CVSS** | CVE-2026-24207: **Critical**; CVE-2026-24206: **High**; remainder: Medium–High |
| **Exploit Maturity** | No active exploitation confirmed as of May 20, 2026 |
| **CISA KEV** | Not listed |
| **Disclosed** | May 18–20, 2026 |
| **Threat Level** | 🟠 HIGH — Critical auth bypass on AI inference infrastructure; no active exploitation but high-value target for AI lab and enterprise ML environments |
| **Admiralty Grade** | A1 — NVIDIA-confirmed and patched |
| **ATT&CK** | T1190 (Exploit Public-Facing Application) · T1068 (EoP) · T1499 (DoS) · T1552 (Credential Access) |

---

## Overview

NVIDIA released a cluster of security patches for **Triton Inference Server** and **TensorRT-LLM** covering authentication bypasses, code execution paths, deserialization vulnerabilities, and resource exhaustion issues. The lead vulnerability (CVE-2026-24207) is a critical authentication bypass that can lead to code execution, privilege escalation, data tampering, and denial of service.

### Why AI Inference Infrastructure Matters

Triton Inference Server is NVIDIA's production-grade inference platform, deployed at scale in:
- Enterprise AI/ML production environments
- Defense and intelligence AI applications
- Research lab inference clusters
- Cloud AI APIs built on NVIDIA infrastructure

Compromise of a Triton server provides:
- Access to models and their weights (IP theft)
- Access to inference request data (potentially sensitive inputs)
- Pivot into the GPU cluster and broader ML infrastructure
- Model poisoning or substitution potential

---

## Key CVEs

| CVE | Type | Severity | Notes |
|---|---|---|---|
| CVE-2026-24207 | Authentication Bypass → RCE/EoP/DoS/InfoDisc | **Critical** | Core inference server; code execution, privesc possible |
| CVE-2026-24206 | Authentication Bypass → EoP/DoS/InfoDisc | High | Secondary auth bypass path |
| CVE-2026-24213 | OOB Read / DALI Backend → RCE/DoS/InfoDisc | High | Data pipeline component |
| CVE-2026-24214 | Integer Overflow / DALI Backend → Code Exec/DoS | High | Data pipeline component |
| CVE-2026-24163 | Unsafe Deserialization / TRT-LLM RPC → RCE/DoS | High | TensorRT-LLM component (requires high priv) |
| CVE-2025-33255 | Unsafe Deserialization / TRT-LLM MPI Server → RCE | High | MPI server component |
| CVE-2026-24142 | Deserialization + Unsafe Handle / TRT-LLM | Medium | Data tampering, InfoDisc |

---

## Mitigations

- **Update immediately** — apply latest NVIDIA Triton Inference Server and TRT-LLM releases per NVIDIA security bulletin
- **Restrict network access** — Triton inference ports should not be publicly exposed; restrict to trusted internal clients
- **Audit deployment configs** — review who has access to inference endpoints and model loading interfaces
- Monitor for unusual model loading, unexpected lateral movement from GPU nodes

---

*Added: 2026-05-20 | NVIDIA security bulletin May 2026 | Patch available — update immediately*
