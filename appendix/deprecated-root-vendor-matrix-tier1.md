# Vendor Matrix — Tier-1 Agentic Identity

**Pack:** Agentic Identity Enterprise Landing (finance-regulated aware)  
**Research date:** 2026-09-15  
**Binding points scored:** Workload identity · User OBO · Agent-as-principal · Shared-SA avoidance (plus IGA/PAM fit)

**Scoring legend (1–5):** 5 = strong, production-proven fit for that binding point; 3 = partial / depends on packaging; 1 = weak or out of scope. Scores are **analyst judgments** grounded in public docs as of 2026-09-15 — mark gaps honestly.

---

## Comparison table (executive)

| Criterion | Microsoft Entra Agent ID | Okta / Auth0 for AI Agents | CyberArk Secure AI Agents (Idira) | AWS IAM + AgentCore / Agentic Lens |
| --- | --- | --- | --- | --- |
| **GA vs preview (honest)** | **Platform GA Apr 2026**; some CA/agent-user features still preview; registry converging to Agent 365 | **Okta for AI Agents + token exchange** documented; **Auth0 XAA Resource App = Early Access** | **Announced GA Dec 2025** (press); docs live (MCP broker, discovery); brand evolving to **Idira** under PANW narrative in 2026 | **IAM primitives GA**; AgentCore Identity & AGENTSEC03 guidance **GA/docs**; maturity is customer implementation |
| **Workload identity** | 4 — Entra workload/agent identities; federation patterns to AWS/GCP | 3 — Agent client auth (keys/JWT assertion); not cloud workload attestation | 3 — Agent registration credentials; federates to external IdP; ZSP via SIA | **5** — IAM roles, IRSA/Pod Identity, Roles Anywhere, STS session policies |
| **User OBO** | 4 — OAuth 2.0 / MCP / A2A; Conditional Access on agents | **5** — XAA / ID-JAG / RFC 8693 token exchange; MCP Enterprise-Managed Authorization | 4 — Broker links human initiator → agent → MCP; OAuth 2.1 | **5** — GetWorkloadAccessTokenForJWT, RFC 8693 OBO, session tags |
| **Agent-as-principal** | **5** — First-class Agent ID, blueprints, sponsors, Agent 365 registry | **5** — Registered AI agents, resource connections, user/machine access | **5** — Inventory + register agent identity; ownership required | 4 — Workload identity per agent; AgentCore auto workload ID; less “directory person-like” IGA than Entra/Okta |
| **Shared SA avoidance** | 4 — Unique Agent IDs; blueprint kill-switch | 4 — Per-agent registration; policy on connections | 4 — Registered agents only to MCP; ZSP reduces standing shared DB creds | **5** — Explicit BP: separate agent vs human roles; SCP prevent assuming human roles |
| **IGA fit** | **5** — Access packages, Lifecycle Workflows for sponsors (GA), Entitlement Management | 4 — Admin-governed connections; Okta IGA/OPA adjacency; agent lifecycle in product | 3 — Ownership + lifecycle states; full IGA often via IdP federation | 2 — CloudTrail/Access Analyzer reviews; IGA is bring-your-own IdP |
| **PAM fit** | 3 — Identity Protection / CA; secrets usually elsewhere | 4 — Vaulted secrets & service accounts via Okta Privileged Access in token-exchange resource types | **5** — Privilege DNA: ZSP, JIT, session/audit via Identity Broker + SIA | 4 — Secrets Manager, short-lived STS; PAM optional partner |
| **MCP posture** | Protocols include MCP; Agent ID as identity foundation | XAA = MCP Enterprise-Managed Authorization extension | Identity Broker in front of registered MCP servers | Guidance + IAM patterns for MCP; not an MCP IdP product |
| **Finance landing fit** | Strong in Microsoft-centric banks; audit via Entra | Strong for multi-SaaS + MCP governance | Strong where privileged data path (DB) needs ZSP | Strong for AWS-native agent platforms |
| **Main gap** | Multi-cloud agents need federation discipline; Agent 365 registry migration | XAA EA limits; product packaging (“Okta for AI Agents” subscription) | MCP-centric; credential rotation limitations in scope docs; vendor brand transition noise | Weaker enterprise agent *directory/IGA* UX without Entra/Okta |

---

## 1. Microsoft Entra Agent ID

### Capabilities
- **Agent identities** as first-class Entra objects: register/manage agents, **blueprints** (class-level permissions/governance), collections/registry (migrating to **Microsoft Agent 365**).
- **Lifecycle:** human **sponsor** required; Lifecycle Workflows for sponsor transfer on leaver (**GA May 2026** per Learn what’s-new).
- **Governance:** Access packages for bundled permissions; Identity Governance adjacency.
- **Protection:** Conditional Access for agents (actors/resources); Identity Protection risk signals; custom security attributes; extended CA for agent *user accounts* noted as **Public Preview (June 2026)**.
- **Protocols:** OAuth 2.0, **MCP**, **A2A** called out at platform GA.
- **Ecosystem:** Copilot Studio / Foundry / Security Copilot auto Agent ID; third-party via sidecar/federation patterns (docs).

### GA vs preview / vapor
| Item | Status (as of 2026-09-15 sources) |
| --- | --- |
| Entra Agent ID **platform** | **GA — April 2026** ([Learn what’s-new](https://learn.microsoft.com/en-gb/entra/fundamentals/whats-new)) |
| Public preview wave / Ignite narrative | Preview late 2025 ([Tech Community 2025-12-17](https://techcommunity.microsoft.com/blog/microsoft-entra-blog/surfing-the-ai-wave-manage-govern-and-protect-ai-agents-with-microsoft-entra-age/2464407)) |
| Agent Registry blades in Entra | **Converging into Agent 365**; Entra registry retirement announced for **2026-05-01** (plan-for-change) |
| Some admin UX (blueprint wizard, etc.) | Docs still label portions **Preview** on GitHub entra-docs what’s-new (ms.date 2026-05-01) |
| Extended CA for agent user accounts | **Public Preview** (June 2026 what’s-new) |

**Not vapor:** GA platform announcement is on Microsoft Learn release notes.  
**Still maturing:** unified Agent 365 control plane, non-Microsoft agent ops at scale, preview CA features.

### Strengths for enterprise landing
- Reuses Entra skills (CA, ID Governance, PIM adjacency).
- Sponsor + blueprint model scales better than one-off app registrations.
- Natural fit if M365 Copilot / Copilot Studio is the agent factory.

### Gaps
- Multi-cloud custom agents need explicit federation/sidecar design.
- Registry API migration (re-register risk) during Agent 365 consolidation.
- PAM/secrets for non-Microsoft tools still need CyberArk/HashiCorp/etc.

### IGA / PAM fit
- **IGA:** Strong (access packages, lifecycle workflows, sponsors).
- **PAM:** Moderate — privilege for *cloud resources* via CA/PIM patterns; not a DB ZSP broker.

### Binding-point scores
| Workload | OBO | Agent-as-principal | Shared-SA avoidance |
| :---: | :---: | :---: | :---: |
| 4 | 4 | 5 | 4 |

### Sources
- https://learn.microsoft.com/en-gb/entra/fundamentals/whats-new (Apr–Jun 2026 entries; retrieved 2026-09-15)
- https://techcommunity.microsoft.com/blog/microsoft-entra-blog/surfing-the-ai-wave-manage-govern-and-protect-ai-agents-with-microsoft-entra-age/2464407 (2025-12-17)
- https://github.com/MicrosoftDocs/entra-docs/blob/main/docs/agent-id/whats-new-agent-id.md (ms.date 2026-05-01)

---

## 2. Okta / Auth0 for AI Agents (incl. Cross App Access / MCP)

### Capabilities
- **Okta for AI Agents:** Register AI agents; configure **user access** and **machine access**; **resource connections** (authz server, secret, service account, resource server STS, **agent-to-agent**).
- **Token exchange:** Subject token → **ID-JAG** → resource access token; supports user-initiated and machine-delegated paths ([Okta Developer guide](https://developer.okta.com/docs/guides/ai-agent-token-exchange/authserver/main/)).
- **Cross App Access (XAA):** Enterprise admin governs agent-to-app / app-to-app; basis of MCP **Enterprise-Managed Authorization** ([Okta newsroom 2025-11-25](https://www.okta.com/newsroom/articles/cross-app-access-extends-mcp-to-bring-enterprise-grade-security-to-ai-agents/); [Auth0 XAA](https://auth0.com/docs/ai-agents-mcp/cross-app-access)).
- **Auth0:** Can act as Resource App AS and/or Requesting App; Token Vault for stored tokens on requesting side.
- **Privileged path:** Resource types for **vaulted secrets** and **service accounts** via Okta Privileged Access.

### GA vs preview / vapor
| Item | Status |
| --- | --- |
| Okta AI agent registration + token exchange docs | **Generally available product docs** requiring “Okta for AI Agents” subscription (retrieved 2026-09-15) |
| Auth0 XAA for Resource App | **Early Access** (explicit in Auth0 docs; EA limitations: 1:1 IdP connection constraints, org limits, no dynamic user creation) |
| XAA as MCP extension | Spec/news milestone **2025-11-25**; SDK rollout called out as progressive |
| Identity Assertion Authorization Grant | Described as **in-progress OAuth extension** in Auth0 docs |

**Honest read:** Core Okta agent governance + ID-JAG flows are real and documented; **Auth0 XAA Resource App remains Early Access** — plan pilots accordingly, not silent production assumptions. Not vapor, but not uniformly GA across Okta vs Auth0 surfaces.

### Strengths
- Best-in-class narrative for **dual entitlement** (human ceiling ∩ agent permission) and killing consent spam.
- Strong multi-SaaS / MCP enterprise control plane story.
- Agent-to-agent as first-class resource type.

### Gaps
- Cloud workload attestation (SPIFFE/IRSA) is adjacent, not native.
- EA limits on Auth0 XAA may constrain complex org topologies.
- Depends on resource apps implementing AS side correctly.

### IGA / PAM fit
- **IGA:** Admin-managed connections replace ad-hoc OAuth grants; pairs with Okta governance products.
- **PAM:** Explicit vaulted secret/service-account resource types — strong for transitional NHI.

### Binding-point scores
| Workload | OBO | Agent-as-principal | Shared-SA avoidance |
| :---: | :---: | :---: | :---: |
| 3 | 5 | 5 | 4 |

### Sources
- https://auth0.com/docs/ai-agents-mcp/cross-app-access (EA; retrieved 2026-09-15)
- https://www.okta.com/newsroom/articles/cross-app-access-extends-mcp-to-bring-enterprise-grade-security-to-ai-agents/ (2025-11-25)
- https://developer.okta.com/docs/guides/ai-agent-token-exchange/authserver/main/ (retrieved 2026-09-15)
- https://www.okta.com/blog/ai/securing-ai-agents-identity-architecture/ (retrieved 2026-09-15)

---

## 3. CyberArk Secure AI Agents / NHI (Idira)

### Capabilities (product docs)
- **Discover** agents across SaaS/cloud/dev (examples: AWS Bedrock, AgentCore, Copilot Studio) into an **AI agents inventory**.
- **Register** agents → assign identity → credentials for MCP access.
- **Identity Broker** between agents and **registered MCP servers**; audit binds **human initiator + agent + tool + MCP target**.
- **Zero standing privileges** for DB access via **Secure Infrastructure Access (SIA)** when MCP server uses that path.
- Lifecycle states: pending, active, suspended, error, delete; **owner required**.

### GA vs preview / vapor
| Item | Status |
| --- | --- |
| Press: Secure AI Agents Solution | Announced **2025-11-04**; **GA December 2025** (footnote in press) |
| Product docs (introduction, scope) | Live on docs.cyberark.com (retrieved 2026-09-15) |
| Brand | 2026 web properties also present **Idira** (Identity Security Platform) / Palo Alto Networks narrative; treat as **platform rebrand/transition** — verify contract SKU names with vendor. *Not evidence the capability is fake.* |
| Scope limits | Docs: up to **1000** agents, MCP servers, tools/server; OAuth 2.1 agents; DB via SIA ZSP (PostgreSQL, SQL Server); **clientId/secret issued once — no in-place rotation** (delete/recreate) |

**Honest read:** Capability is GA-timed and documented; initial release is **MCP-broker + discovery + ZSP DB** focused — not a full replacement for IdP agent directory alone. Credential rotation limitation is a real operational gap for finance IR teams.

### Strengths
- Privilege-centric: ZSP/JIT mindset applied to agents.
- Excellent **session/tool audit** story for examiners.
- Complements Entra/Okta rather than only competing.

### Gaps
- MCP-centric enforcement; non-MCP tool planes need other controls.
- Agent client secrets: no rotate-in-place per scope doc.
- Requires SIA enabled for DB ZSP path; federation to external IdP for workforce SSO.
- Vendor naming (CyberArk vs Idira) may confuse procurement RFP language in 2026.

### IGA / PAM fit
- **PAM:** **Core strength** (broker, ZSP, privileged session culture).
- **IGA:** Ownership + inventory; deep joiner/mover/leaver often still IdP/IGA tool.

### Binding-point scores
| Workload | OBO | Agent-as-principal | Shared-SA avoidance |
| :---: | :---: | :---: | :---: |
| 3 | 4 | 5 | 4 |

### Sources
- https://www.cyberark.com/press/cyberark-introduces-first-identity-security-solution-purpose-built-to-protect-ai-agents-with-privilege-controls/ (2025-11-04)
- https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm (retrieved 2026-09-15)
- https://docs.cyberark.com/manage/latest/en/content/secureai/scope.htm (retrieved 2026-09-15)
- https://www.cyberark.com/product-insights/cyberark-secure-ai-agents-a-closer-look-at-new-solution-capabilities/ (retrieved 2026-09-15)

---

## 4. AWS IAM workload identity + agentic AI security guidance

### Capabilities
- **Workload identity primitives:** IAM roles, **STS** short-lived creds, **EKS IRSA** and **Pod Identity**, **IAM Roles Anywhere** (X.509 for outside AWS), permission boundaries, session policies, SCPs, Access Analyzer.
- **Agentic guidance:** Well-Architected **Agentic AI Lens — AGENTSEC03** maturity model (shared keys → dedicated roles → AgentCore Identity → OBO with GetWorkloadAccessTokenForJWT → optimized continuous least privilege).
- **Bedrock AgentCore Identity:** Workload identity per deployed agent; token vault; OBO / token exchange to external SaaS; inbound JWT authorizer with custom claims ([AWS Security Blog 2026-08-19](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/)).
- Patterns: `AssumeRoleWithWebIdentity` + session tags for user-scoped data plane; never have agent assume human IdC roles.

### GA vs preview / vapor
| Item | Status |
| --- | --- |
| IAM / STS / IRSA / Roles Anywhere | **Long-GA** building blocks |
| EKS Pod Identity | GA (multi-year); coexistence with IRSA |
| Agentic AI Lens AGENTSEC03 | Published WA guidance (retrieved 2026-09-15) |
| Bedrock AgentCore Identity features in blog | Documented production patterns (2026-08-19); treat as **current platform capability** — confirm regional SKU/GA in AWS console for your region |

**Honest read:** No “Agent ID directory product” equivalent to Entra; strength is **cloud PEP + OBO patterns**. Not vapor — but enterprises still need IdP/IGA for org-wide agent inventory.

### Strengths
- Best control of *data-plane* authorization when resources are AWS-native.
- Clear anti-patterns documented (shared API keys, agent≈human role blur, filtering-only authz).
- Fits regulated AWS landing zones / Control Tower / SCPs.

### Gaps
- Cross-SaaS admin connection governance weaker than Okta XAA without extra IdP.
- Agent sponsor/IGA UX not native.
- Metadata filtering in Knowledge Bases is **application-layer** (AWS blog notes IAM cannot see filter content) — important residual risk.

### IGA / PAM fit
- **IGA:** Bring Entra/Okta; use tags + Access Analyzer + review cadence.
- **PAM:** Secrets Manager + partner PAM; JIT via temporary elevation patterns.

### Binding-point scores
| Workload | OBO | Agent-as-principal | Shared-SA avoidance |
| :---: | :---: | :---: | :---: |
| 5 | 5 | 4 | 5 |

### Sources
- https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentsec03.html (retrieved 2026-09-15)
- https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/ (2026-08-19)
- AWS EKS Pod Identity / IRSA docs (retrieved via search 2026-09-15)

---

## 5. How to combine (typical finance enterprise)

**Speculation marked:** Common reference architectures in 2026 RFPs (not a standard):

1. **Entra or Okta** = agent directory, OBO/XAA, workforce SSO, IGA.  
2. **AWS IAM / AgentCore** (if on AWS) = workload identity + data-plane PEP.  
3. **CyberArk/Idira Secure AI Agents** = privileged MCP/DB path, ZSP, tool-session audit.  

Avoid buying three overlapping “agent ID” registries without a **system of record** decision (usually the enterprise IdP + Agent 365 or Okta agent inventory).

---

## 6. Score summary (binding points only)

| Vendor | Workload | OBO | Agent principal | Anti-shared-SA | Total /20 |
| --- | :---: | :---: | :---: | :---: | :---: |
| Microsoft Entra Agent ID | 4 | 4 | 5 | 4 | **17** |
| Okta / Auth0 AI Agents | 3 | 5 | 5 | 4 | **17** |
| CyberArk Secure AI Agents | 3 | 4 | 5 | 4 | **16** |
| AWS IAM + AgentCore guidance | 5 | 5 | 4 | 5 | **19** |

*Totals are not “pick a winner” — they reflect different layers. AWS leads on cloud workload PEP; Entra/Okta lead on enterprise agent directory + governed delegation; CyberArk leads on privileged tool-path PAM.*

---

*End of 02-vendor-matrix-tier1.md*
