# Agentic Identity Research Pack (lab-bot)

**Audience:** Kenny (Security Engineer — IAM + agentic AI, CLSA / finance-relevant)  
**As-of:** 2026-09-15  
**Scope:** Docs only — architecture → identity-binding maps, controls, and regulated implications. No code, Docker, or repos.

## Purpose

Map how AI agent **runtime shapes**, **tool/action planes**, and **control topologies** determine where identity must bind, where a PEP can enforce, and which finance-relevant controls (SoD, audit, HITL, data egress) apply. Prefer primary sources (vendor docs, IETF/OpenID drafts, OWASP, CSA) from 2025–2026; mark uncertainty explicitly.

## Files

| File | Status | Description |
|------|--------|-------------|
| `01-architecture-identity-binding.md` | **Written** | Architecture patterns → identity binding map (runtime, tool plane, acts-as models, trust topology, failure modes) |
| `02-standards-and-protocols.md` | Planned | OAuth/OIDC, MCP auth, AuthZEN/COAZ, SPIFFE/WIMSE, IETF agent-delegation drafts |
| `03-vendor-platform-controls.md` | Planned | Entra Agent ID / Copilot Studio, Bedrock AgentCore Identity, Google Agent Identity — control inventories |
| `04-finance-regulatory-implications.md` | Planned | SoD, audit trails, data residency/egress, HITL for material actions, IGA for non-human identities |
| `05-threats-and-controls-matrix.md` | Planned | ASI03 / confused deputy / privilege inflation → control mappings |

## Method

- WebSearch + WebFetch of primary sources (prefer 2025–2026)
- Accurate over encyclopedic; cite URL + approximate retrieval/publication date
- Uncertainty marked where drafts, GA/preview status, or channel gaps matter

## Notes

This pack lives under `from-lab-bot/` to avoid collision with parallel workers writing sibling folders under `/workspace/agentic-identity-research/`.
