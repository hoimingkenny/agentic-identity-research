# Agentic Identity Research Pack (lab-bot)

**Audience:** Kenny (Security Engineer — IAM + agentic AI, finance-relevant)  
**As-of:** 2026-09-15  
**Scope:** Docs only — architecture → identity-binding maps, controls, and regulated implications. No code, Docker, or repos.

## Purpose

Map how AI agent **runtime shapes**, **tool/action planes**, and **control topologies** determine where identity must bind, where a PEP can enforce, and which finance-relevant controls (SoD, audit, HITL, data egress) apply. Prefer primary sources (vendor docs, IETF/OpenID drafts, OWASP, CSA) from 2025–2026; mark uncertainty explicitly.

## Files

| File | Status | Description |
|------|--------|-------------|
| `01-architecture-identity-binding.md` | **Written** | Architecture patterns → identity binding map (runtime, tool plane, acts-as models, trust topology, failure modes) |
| `02-vendor-matrix-tier1.md` | **Written** | Tier-1 vendor deep-dive + scores (Entra, Okta/Auth0, CyberArk, AWS) |
| `03-landing-playbook-skeleton.md` | **Written** | Phased program skeleton (phases 0–10, binding controls) |
| `04-okta-ping-ibm-iga-vs-agentic.md` | **Written** | Okta vs Ping Identity vs IBM — traditional IGA vs agentic identity (with SailPoint IIQ baseline) |
| `05-macro-ai-security-vs-governance.md` | **Written** | AI security (L1) vs agentic identity governance (L4) — why IIQ + AccuKnox + new layer |
| `06-management-1pager-scope-lock.md` | **Written** | Management 1-pager for L4 agentic identity (enterprise finance context; scope-locked 2026-09-22) |
| `07-shortlist-poc-matrix-skeleton.md` | **Written** | Shortlist / PoC capability matrix (skeleton for vendor comparison) |

## Method

- WebSearch + WebFetch of primary sources (prefer 2025–2026)
- Accurate over encyclopedic; cite URL + approximate retrieval/publication date
- Uncertainty marked where drafts, GA/preview status, or channel gaps matter

## Notes

This pack lives under `from-lab-bot/` to avoid collision with parallel workers writing sibling folders under `/workspace/agentic-identity-research/`.
