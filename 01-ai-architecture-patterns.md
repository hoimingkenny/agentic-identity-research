# AI Architecture Patterns → Identity Controls

**Pack:** Agentic Identity Enterprise Landing (finance-regulated aware)  
**Research date:** 2026-09-15  
**Scope:** Docs/research only — no application code or scaffolds.

This document maps **how agents run** to **where credentials live** and **which identity controls must bind**, with finance/regulated notes. Speculation is marked explicitly.

---

## 1. Taxonomy

### 1.1 Runtime shapes

| Shape | How it runs | Typical credential surface | Primary identity binding |
| --- | --- | --- | --- |
| **Chat + tools** | Interactive session; user present; tool calls synchronous or short async | User session token + agent client credentials; tool tokens minted per call | User OBO ∩ agent-as-principal |
| **Long-running / autonomous** | Schedule, event, alarm, or queue-driven; user often absent | Workload identity (IAM role, SPIFFE, managed identity); optional stored OAuth refresh / vaulted secrets | Workload identity; escalate via JIT + HITL for high impact |
| **Multi-agent** | Planner/worker/sub-agents; agent-to-agent (A2A) hops | Per-agent principals; token exchange at each hop; nested `act` / actor claims | Agent-as-principal + attenuated delegation; ban shared SA across agents |
| **SaaS-embedded** | Copilot Studio, Bedrock AgentCore, vendor “agent” products inside M365/AWS/etc. | Platform-issued agent/workload IDs; vendor OAuth to SaaS APIs | Platform agent ID + enterprise IdP policy (XAA / Entra Agent ID / broker) |
| **Custom cloud** | Self-built orchestration on K8s/serverless | IRSA/Pod Identity, Roles Anywhere, SPIFFE/SPIRE, Entra workload ID | Workload identity first; OBO when user-initiated |

### 1.2 Tool planes

| Plane | Mechanism | Credential / auth pattern | Confused-deputy risk |
| --- | --- | --- | --- |
| **Function calling** | LLM selects JSON tool; runtime executes in-process or via RPC | Often inherits host process identity or shared API keys | High if tools run with overbroad host SA |
| **MCP (Model Context Protocol)** | Agent as MCP client → MCP servers expose tools | OAuth 2.1 resource server model; Enterprise-Managed Authorization / XAA; brokers | High without dual (user + agent) entitlement checks |
| **RPA** | UI automation against apps without APIs | Bot accounts, vaulted passwords, session recording | Very high; treat as privileged NHI + PAM |
| **Direct API** | REST/gRPC with scoped tokens | Client credentials, OBO/token exchange, mTLS | Medium if audience-restricted + short-lived |

### 1.3 Identity binding points (canonical)

These four binding points are the scoring axes used in `02-vendor-matrix-tier1.md`:

1. **Workload identity** — Attests *which runtime/workload* is calling (IAM role, managed identity, SPIFFE JWT-SVID, Entra workload ID). Answers: “Is this a legitimate agent process?”
2. **User OBO (on-behalf-of)** — Propagates *user* subject via RFC 8693 token exchange / ID-JAG / OBO without the agent holding the user’s password or full session. Answers: “For whom is this action authorized?”
3. **Agent-as-principal** — First-class directory object / registry entry for the agent with its own ID, sponsor, scopes, lifecycle. Answers: “Which agent acted?”
4. **Shared service account (anti-pattern)** — One bot/SA used by many agents or humans. Answers (badly): “Someone/something did this.” Prefer **ban or strictly quarantine**.

**Composition (industry consensus, not vendor-specific):**  
SPIFFE/workload identity proves the *actor workload*; OAuth 2.1 + RFC 8693 / Identity Assertion JWT Authorization Grant (ID-JAG) carries *delegation*; MCP treats servers as OAuth resource servers. See NIST NCCoE concept work (2026) and practitioner write-ups below.

### 1.4 Trust topology

```
[Human User] --SSO/OIDC--> [Agent Runtime / Workload ID]
                |                    |
                | OBO / ID-JAG       | client assertion / SVID
                v                    v
         [Enterprise IdP / AS] <--> [Token exchange / Broker]
                                         |
                    audience-scoped AT   |
                                         v
                              [Tool / MCP Server / API]
                                         |
                                         v
                              [Downstream PEP: IAM / SaaS RBAC / DB ZSP]
```

- **Trust anchors:** Enterprise IdP, workload attestation (node/K8s/cloud), optional PAM broker for secrets/ZSP.
- **Policy enforcement points (PEPs):** IdP (connection policy), agent gateway/broker, MCP server, cloud IAM, application ABAC.
- **Non-goal:** Trusting the LLM prompt as an authorization boundary.

### 1.5 Human-in-the-loop (HITL)

| Pattern | Use when | Finance note |
| --- | --- | --- |
| **Pre-approved flight plan / access package** | Recurring low–medium risk flows | Prefer over per-tool popups; auditable entitlement |
| **Step-up / JIT for high-impact tools** | Payments, wire, customer PII export, model/config change | Align with maker-checker / dual control |
| **Elicitation (MCP)** | Clarifying task intent | **Do not** use to solicit passwords/secrets (MCP guidance; NIST 2026 blog) |
| **Consent fatigue risk** | Chatty agents asking “Allow?” constantly | Same failure mode as MFA bombing — undermines non-repudiation |

### 1.6 Failure modes

| Failure | Description | Mitigations |
| --- | --- | --- |
| **Confused deputy** | Agent/gateway holds powerful credential; any caller who can invoke it inherits that power | Dual check: user entitlement ∩ agent entitlement; never forward SA without entitlement gate ([arxiv gateway paper](https://arxiv.org/html/2608.10760)) |
| **Overbroad scopes** | Agent role = “admin of everything the demo needed” | Permission boundaries, RAR/granular scopes, session policies, tool allowlists |
| **Sub-agent inflation** | Planner spawns unbounded workers; each inherits parent token | Per-sub-agent principal or attenuated token per hop; registry quotas; kill-switch on blueprint |
| **Shadow agents** | Unregistered Copilots, local IDE agents, personal SaaS agents with user creds | Discovery/inventory; block unsponsored agents; DLP on credential paste; local harness/sandbox |
| **Credential sharing** | User pastes password/API key into agent | Ban pattern; NIST emphasizes first-class agent identity ([NIST blog 2026-08-27](https://www.nist.gov/blogs/cybersecurity-insights/back-future-why-agentic-ai-needs-strong-identity-foundation)) |
| **Static long-lived tokens** | API keys in `.env` / markdown | Short-lived, audience-restricted, DPoP/mTLS where possible |

---

## 2. Pattern deep-dives

### 2.1 Chat + tools (interactive)

| Dimension | Guidance |
| --- | --- |
| **How it runs** | User authenticates to chat front-end; agent runtime receives user JWT; tools invoked in-session. |
| **Where credentials live** | User tokens in front-end/session store (short-lived); agent client keys in secret store / platform identity; tool ATs minted per call via OBO/XAA; prefer **no** long-lived SaaS refresh tokens in agent memory. |
| **Controls that must bind** | Agent-as-principal + User OBO; Conditional Access / risk on both user and agent; tool-level RBAC at MCP/API. |
| **Finance / regulated** | Preserve subject + actor in audit (who asked, which agent, which tool). Segregation of duties: agent that proposes a payment ≠ identity that releases funds without maker-checker. |

**Sources:** AWS Security Blog on AgentCore user-context propagation (2026-08-19); Okta XAA / MCP Enterprise-Managed Authorization (2025-11-25).

### 2.2 Long-running / autonomous agents

| Dimension | Guidance |
| --- | --- |
| **How it runs** | Triggered by EventBridge/queue/cron; may chain for hours/days. |
| **Where credentials live** | Cloud workload identity (IAM role, managed identity); vaulted secrets only via PAM/JIT; avoid embedding user refresh tokens for unattended runs unless explicitly governed “machine access” delegation. |
| **Controls that must bind** | Workload identity primary; agent-as-principal for audit; SCPs/permission boundaries; optional scheduled access reviews faster than human cadence (AWS AGENTSEC03). |
| **Finance / regulated** | Map to batch/job controls: change management for agent prompts/tools; immutable logs; kill-switch; no silent privilege expansion when tool fails with AccessDenied. |

**Sources:** [AWS Well-Architected Agentic AI Lens — AGENTSEC03](https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentsec03.html) (retrieved 2026-09-15).

### 2.3 Multi-agent systems

| Dimension | Guidance |
| --- | --- |
| **How it runs** | Supervisor delegates to specialists; A2A protocols hand off tasks. |
| **Where credentials live** | Each agent: own workload ID + own OAuth client; hop uses token exchange with nested actor claims (Ping/SPIFFE pattern). |
| **Controls that must bind** | Agent-as-principal per node; forbid single shared SA; attenuate scopes each hop; registry limits on spawn rate. |
| **Finance / regulated** | End-to-end provenance: user → agent A → agent B → tool. Sub-agent inflation = control failure for SOX-style accountability. |

**Sources:** Ping Identity developer blog on token exchange + SPIFFE (retrieved 2026-09-15); Okta agent-to-agent ID-JAG flows ([Okta Developer](https://developer.okta.com/docs/guides/ai-agent-token-exchange/authserver/main/)).

### 2.4 SaaS-embedded agents

| Dimension | Guidance |
| --- | --- |
| **How it runs** | Built in Copilot Studio / vendor agent builders; run on vendor control plane; call M365/Salesforce/etc. |
| **Where credentials live** | Platform-issued Agent IDs / workload identities; Graph/SaaS tokens via OBO or app permissions; admin-governed connections (XAA) preferred over user-consent sprawl. |
| **Controls that must bind** | Agent-as-principal in enterprise directory (Entra Agent ID / Okta AI Agents); IGA access packages; discovery for shadow SaaS agents. |
| **Finance / regulated** | Data residency & subprocessors; DLP on tool outputs; admin-managed connections over end-user OAuth consent for production finance apps. |

**Sources:** Microsoft Entra Agent ID (GA April 2026 per Learn what’s-new); Okta Cross App Access as MCP extension (2025-11-25).

### 2.5 Custom cloud agents

| Dimension | Guidance |
| --- | --- |
| **How it runs** | Customer-built on EKS/ECS/Lambda/VMs; own orchestration. |
| **Where credentials live** | IRSA or EKS Pod Identity; IAM Roles Anywhere for non-AWS; SPIFFE optional; Secrets Manager / PAM for non-IAM secrets. |
| **Controls that must bind** | Workload identity mandatory; separate agent vs human roles (SCP guardrails); OBO when user-initiated. |
| **Finance / regulated** | Align with existing cloud control frameworks (FFIEC CAT, SOC2 CC6, ISO 27001 A.5/A.8 analogues): unique IDs, least privilege, logging, revocation. |

**Sources:** AWS AGENTSEC03; EKS Pod Identity / IRSA guidance (AWS docs).

---

## 3. Tool-plane specifics

### 3.1 Function calling

- Bind tools to **explicit allowlists** per agent blueprint.
- Execute tools under **dedicated** identity, not the chat UI’s user OS account.
- Log tool name, args hash, agent ID, user ID, decision (allow/deny).

### 3.2 MCP

- Treat MCP servers as **OAuth 2.1 resource servers** (MCP auth revisions through 2025).
- Prefer **Enterprise-Managed Authorization (XAA)** for enterprise connectors so IT policy replaces repetitive user consent ([Okta newsroom 2025-11-25](https://www.okta.com/newsroom/articles/cross-app-access-extends-mcp-to-bring-enterprise-grade-security-to-ai-agents/); [Auth0 XAA docs](https://auth0.com/docs/ai-agents-mcp/cross-app-access) — Early Access as of docs retrieval 2026-09-15).
- Broker patterns (e.g., CyberArk/Idira Identity Broker) add ZSP and session audit between agent and MCP ([CyberArk Secure AI docs](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm)).

### 3.3 RPA

- Classify RPA bots as **privileged NHIs**; vault credentials; session monitoring; never share bot password across agents.
- Prefer API/MCP over RPA for regulated data paths when available.

### 3.4 Direct API

- Audience-restricted access tokens; short TTL; DPoP or mTLS sender-constraining where supported.
- RFC 8693 exchange at each trust boundary rather than replaying the portal token downstream.

---

## 4. Mapping matrix: pattern × binding point

| Pattern | Workload ID | User OBO | Agent-as-principal | Shared SA |
| --- | --- | --- | --- | --- |
| Chat + tools | Required for runtime | **Required** | Required | Forbidden |
| Long-running | **Primary** | Optional (if delegated machine access) | Required | Forbidden |
| Multi-agent | Per agent | Propagate + attenuate | Per agent | Forbidden |
| SaaS-embedded | Platform-managed | Via XAA/OBO | Directory Agent ID | Forbidden |
| Custom cloud | **Primary** (IRSA/RA/SPIFFE) | When user-initiated | Required | Forbidden |

---

## 5. Finance / regulated overlay (honest framing)

**What is settled practice (apply now):**
- Unique, attributable agent identities (not shared human passwords).
- Least privilege, short-lived credentials, revocation.
- Audit: human initiator + agent identity + tool + target resource.
- HITL for high-impact financial actions with dual control where existing policy requires it.
- Inventory/discovery to reduce shadow agents.

**What is still maturing (label as emerging):**
- Uniform cross-vendor “agent ID” standards (NCCoE project in planning after Feb 2026 concept paper).
- Consumer-style agent authenticators bound to users (FIDO exploration — early).
- Universal RAR/transaction-token adoption across all SaaS tool planes.

**Speculation (marked):**  
Over 2026–2027, finance regulators are likely to treat unmanaged agent credentials similarly to unmanaged service accounts in exams (shared secrets, orphan bots, missing ownership). *This is inference from examiner patterns on NHIs, not a cited regulation text.*

---

## 6. Sources (URLs and dates)

| Source | Date / status | URL |
| --- | --- | --- |
| Microsoft Entra what’s new — Agent ID platform GA | April 2026 (Learn page; retrieved 2026-09-15) | https://learn.microsoft.com/en-gb/entra/fundamentals/whats-new |
| Microsoft Tech Community — Entra Agent ID preview narrative | 2025-12-17 (updated 2026-03-31) | https://techcommunity.microsoft.com/blog/microsoft-entra-blog/surfing-the-ai-wave-manage-govern-and-protect-ai-agents-with-microsoft-entra-age/2464407 |
| Auth0 Cross App Access (XAA) docs | Early Access; retrieved 2026-09-15 | https://auth0.com/docs/ai-agents-mcp/cross-app-access |
| Okta — XAA as MCP Enterprise-Managed Authorization | 2025-11-25 | https://www.okta.com/newsroom/articles/cross-app-access-extends-mcp-to-bring-enterprise-grade-security-to-ai-agents/ |
| Okta Developer — AI agent token exchange | Retrieved 2026-09-15 | https://developer.okta.com/docs/guides/ai-agent-token-exchange/authserver/main/ |
| CyberArk Secure AI Agents introduction | Docs retrieved 2026-09-15 | https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm |
| CyberArk Secure AI Agents scope | Docs retrieved 2026-09-15 | https://docs.cyberark.com/manage/latest/en/content/secureai/scope.htm |
| CyberArk press — Secure AI Agents GA timing | 2025-11-04 (GA Dec 2025) | https://www.cyberark.com/press/cyberark-introduces-first-identity-security-solution-purpose-built-to-protect-ai-agents-with-privilege-controls/ |
| AWS AGENTSEC03 — Agent identity and permission management | Retrieved 2026-09-15 | https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentsec03.html |
| AWS Security Blog — Propagate user authz context (AgentCore) | 2026-08-19 | https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/ |
| NIST blog — Agentic AI identity foundation | 2026-08-27 | https://www.nist.gov/blogs/cybersecurity-insights/back-future-why-agentic-ai-needs-strong-identity-foundation |
| NIST NCCoE concept paper listing | 2026-02-05 (ipd) | https://csrc.nist.gov/pubs/other/2026/02/05/accelerating-the-adoption-of-software-and-ai-agent/ipd |
| arXiv — Enterprise MCP gateway / confused deputy | 2026 (html 2608.10760) | https://arxiv.org/html/2608.10760 |
| Okta blog — Securing AI agents identity architecture | Retrieved 2026-09-15 | https://www.okta.com/blog/ai/securing-ai-agents-identity-architecture/ |
| Dreaming.press — SPIFFE + OBO for agents | Retrieved 2026-09-15 | https://dreaming.press/posts/spiffe-spire-workload-identity-for-ai-agents.html |

---

*End of 01-ai-architecture-patterns.md*
