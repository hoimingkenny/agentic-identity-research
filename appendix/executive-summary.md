# Executive summary — Agentic identity enterprise landing research

**As of:** 2026-09-15  
**Audience:** Finance/enterprise-applicable + personal learning  
**Status:** Docs-only research pack v1 (no code/sandbox)

## Purpose
Research how to land **agentic identity** in the enterprise: map **AI agent architectures** to **identity binding points**, analyze **Tier-1 vendor tools**, and outline a **landing playbook**.

## Pack layout
| Path | Role |
|------|------|
| `01-ai-architecture-patterns.md` | Architecture taxonomy → controls |
| `02-vendor-matrix-tier1.md` | Tier-1 vendor deep-dive + scores |
| `03-landing-playbook-skeleton.md` | Phased program skeleton |
| `from-lab-bot/` | Parallel deeper binding-point matrix (merge source) |
| `README.md` | Index |

## Canonical binding points
1. **Workload identity** — which runtime/process is calling  
2. **User OBO / delegation** — on whose behalf (not credential sharing)  
3. **Agent-as-principal** — first-class agent identity + lifecycle  
4. **Eliminate shared service accounts** — standing shared secrets are the anti-pattern  

Cloud identity answers (1). Agentic IAM answers (2)+(3). Conflating them fails.

## Architecture → identity (why this comes first)
Prescribe controls only after knowing runtime shape:
- Interactive chat + tools / MCP  
- Long-running / scheduled agents  
- Multi-agent (orchestrator + sub-agents)  
- SaaS-embedded (Copilot-class) vs custom cloud agents  

**Where a PEP can sit:** model harness, MCP gateway, API gateway, IdP token mint, cloud IAM, PAM broker.  
**Dominant finance failure modes:** confused deputy, overbroad tool scopes, sub-agent privilege inflation, shadow agents, static long-lived tokens.

## Tier-1 vendor snapshot (honest GA)
| Vendor | Posture | Best at | Main gap |
|--------|---------|---------|----------|
| **Microsoft Entra Agent ID** | Platform **GA Apr 2026**; some CA/UX still preview; registry→Agent 365 in flux | Agent directory, sponsors/blueprints, IGA adjacency, M365 agent factories | Multi-cloud federation discipline; not a DB ZSP broker |
| **Okta / Auth0** | Split stack: Auth for MCP + OBO **GA**; Agent-as-Principal / XAA often **EA**; Okta for AI Agents + Agent SSO **GA** paths | Governed delegation (XAA/ID-JAG), MCP enterprise-managed auth story | EA limits; Auth0 vs Okta packaging complexity |
| **CyberArk / Idira Secure AI Agents** | **GA path** (~Dec 2025+) for Secure AI Agents; 2026 Idira/PANW branding noise — confirm SKU | Privileged MCP/DB path, discovery, ZSP/JIT, audit (weakens in passthrough) | Not a universal workforce IdP; AuthZ grain varies by MCP mode |
| **AWS (STS/IRSA/Roles Anywhere + AgentCore guidance)** | Workload identity **GA**; agent IGA directory **not** AWS’s product | Cloud PEP, short-lived roles, OBO patterns into AWS resources | Bring-your-own IdP/IGA for agent lifecycle/certification |

**Compose, don’t crown one vendor:** IdP (Entra or Okta) = agent directory + OBO/XAA; AWS = workload/data-plane; CyberArk/Idira = privileged tool/MCP path; SailPoint (Tier-2) = JML/certification for NHIs/agents.

## Biggest control gaps (research, not lab mandates)
- **Tool/argument-level AuthZ** still mostly app-side or early (e.g. FGA previews)  
- **EA features** (XAA resource app, some agent-as-principal) — pilot carefully in banks  
- **Audit fidelity** drops when brokers run passthrough  
- **AWS ≠ IGA** — cloud identity alone does not land enterprise agent ownership/cert campaigns  

## Landing sequence (skeleton)
1. **Discover** agent/MCP inventory, shared SAs, owners  
2. **Pilot** dual paths: attended OBO + bounded autonomous  
3. **Enforce** unique agent IDs, short-lived creds, revoke/kill paths  
4. **Integrate** IGA JML + PAM ZSP for material actions  
5. **Scale** blueprints, spawn quotas, metrics (e.g. MTTRevoke, % static keys)  

HITL is necessary but fragile — prefer pre-approved access packages / flight plans + step-up for material actions over consent spam.

## What we are *not* doing yet
No Docker lab, no competing mini-Okta, no NHI inventory product. A thin failure→fix sandbox stays **gated** until the matrix names a gap worth proving hands-on.

## Suggested next research (when you want)
- Tier-2: SailPoint, Google Agent Identity (SPIFFE), Ping/Cloudflare  
- Standards skim: MCP auth, XAA/ID-JAG, RFC 8693  
- Enterprise-oriented memo: map current SailPoint + CyberArk + AWS posture to a 90-day pilot  
