# AI Agent Architecture Patterns → Identity Binding Map

**Audience:** Enterprise / finance IAM (Kenny — finance-relevant)  
**As-of:** 2026-09-15  
**Doc type:** Architecture → binding map (docs only)  
**Method:** Primary sources via WebSearch/WebFetch (2025–2026 preferred). Citations include URLs; dates are publication or last-updated where known, else retrieval date.

---

## 0. Executive framing

Enterprise agent deployments fail identity when they treat "the LLM called a tool" as a single principal. In practice there are **at least four distinct binding surfaces**:

1. **Workload / runtime identity** — who is this agent process? (SPIFFE SVID, cloud agent principal, Entra Agent ID, Bedrock workload identity)
2. **Acts-as / delegation identity** — on whose authority does this call proceed? (user OBO, agent-as-principal, shared service account)
3. **Tool / action authorization** — which operation on which resource, with which arguments? (OAuth scopes are coarse; AuthZEN/COAZ or API gateway PDP is fine-grained)
4. **Session / memory isolation** — which user's context may this runtime see? (multi-user shared memory is the hard case)

Finance/regulated implication: audit and SoD require **attributable dual identity** (agent + human/sponsor) on material actions, short-lived constrained tokens, and HITL gates for irreversible or high-value operations. Shadow agents outside IGA break all of the above.

**Uncertainty:** Several cited IETF drafts (AIP, PEDIGREE, WIMSE delegation) and OpenID COAZ-MCP are implementers' drafts — useful for threat models and design targets, not yet "mandate compliance."

---

## 1. Runtime shape → binding map

### 1.1 Interactive chat + tools

**Shape:** Human-present session; agent proposes tool calls; user may approve or continue conversationally.

| Binding point | What matters | Typical controls |
|---------------|--------------|------------------|
| Session AuthN | End-user IdP session (OIDC) | Conditional Access / MFA; channel-bound auth (e.g. Teams vs web) |
| Agent principal | Distinct agent identity vs shared bot SA | Per-agent Entra Agent ID / cloud agent principal; no shared SA across agents |
| Acts-as | Usually **user OBO** for user-owned data | OAuth OBO / 3LO; consent; step-up for elevated scopes |
| Tool PEP | Before each `tools/call` | Harness allowlist; MCP gateway + AuthZEN PDP; connector DLP |
| Memory | Per-user / per-session store | Tenant + user key; wipe between sessions |

**Finance implications:** Interactive channel is the natural place for HITL. SoD: maker who configures connectors must not be sole approver of production publish + production data access. Audit: log user, agent ID, tool name, resource IDs, decision (permit/deny/HITL).

**Platform anchors:**
- Microsoft Copilot Studio: new agents get **Entra Agent IDs** (Agent subtype service principals); connectors commonly use **OBO**; connector scopes appear on Agent ID at publish; Conditional Access on agent identity currently end-to-end mainly for **Teams** channel (other channels may still use Power Platform connector auth — governance visibility ≠ uniform enforcement). Sources: [Entra Agent IDs](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-use-entra-agent-identities) (retrieved 2026-09-15); [App registration / Agent IDs](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-certificates-configuration-values) (retrieved 2026-09-15); [OBO custom connectors](https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-custom-connector-on-behalf-of) (retrieved 2026-09-15).
- Google Agent Identity: SPIFFE-based per-agent principal; auth manager holds 3LO user tokens; optional pattern where **gateway decrypts** end-user creds so agent never sees raw credential. Source: [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview) (last updated 2026-09-14).

### 1.2 Long-running / scheduled agents

**Shape:** Cron, event-driven, or multi-hour workflows; human often **not** present at action time.

| Binding point | What matters | Typical controls |
|---------------|--------------|------------------|
| Workload AuthN | Runtime attestation | SPIFFE/SPIRE; cloud workload identity; AgentCore SLR |
| Acts-as | Often **agent-as-principal** or **pre-consented OBO** with cached refresh | Prefer task-scoped tokens minted at job start; expire with job |
| Token lifetime | Standing privileges = high blast radius | JIT / just-enough; no long-lived API keys in agent memory |
| HITL | Must be **asynchronous** (CIBA, ticket, dual-control queue) | Gate high-value actions; deny-by-default if approval timeout |
| Memory | Durable state across runs | Encrypt at rest; bind to agent ID + purpose; purge on decommission |

**Finance implications:** Scheduled agents that trade, post journals, or move money need SoD between (a) job definition, (b) credential grant, (c) approval of material outputs. Pre-consented refresh tokens without re-validation are a classic ASI03 risk (OWASP Agentic Top 10, Dec 2025 / 2026 edition).

**Platform anchors:**
- AWS Bedrock **AgentCore Identity**: workload identities for agents; inbound JWT or SigV4; outbound OAuth via credential providers / token vault keyed by workload identity + user ID. Prefer `GetWorkloadAccessTokenForJWT` over `GetWorkloadAccessTokenForUserId` in production (latter does **not** cryptographically verify userId). Sources: [AgentCore Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html); [Inbound/Outbound Auth](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-oauth.html) (retrieved 2026-09-15). SLR `AWSServiceRoleForBedrockAgentCoreRuntimeIdentity` for agents created on/after **2025-10-13**.

### 1.3 Multi-agent orchestration

**Shape:** Planner / supervisor delegates to specialist sub-agents; tools and privileges differ per node.

| Binding point | What matters | Typical controls |
|---------------|--------------|------------------|
| Per-agent identity | Each node has its own principal | No shared SA; IAM bindings per agent principal |
| Delegation hop | Authority must **attenuate**, not inflate | Cryptographic delegation chain (IETF drafts); PDP check per hop |
| Inter-agent trust | Default trust of "internal" messages = confused deputy | Re-authZ original user intent at each privileged hop |
| Audit | Full provenance chain | Nested `act` claims + enforced attenuation (RFC 8693 alone is insufficient — see §5) |

**Finance implications:** Sub-agent privilege inflation is the multi-agent form of SoD failure — a low-privilege triage agent must not induce a payments agent to execute without independent authorization of amount, account, and purpose.

**Sources / drafts:**
- OWASP ASI03 Identity & Privilege Abuse — cross-agent confused deputy. [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/download/52117) (released ~Dec 2025).
- IETF: [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/) — offline verifiable attenuation; notes RFC 8693 `act` is informational only.
- IETF: [draft-sweeney-wimse-credential-delegation](https://datatracker.ietf.org/doc/draft-sweeney-wimse-credential-delegation/) — RFC 8693 + DPoP + RAR + CIBA profile; cites **delegation chain splicing** (disclosed OAuth WG ~2026-02-26).
- IETF: [PEDIGREE](https://www.ietf.org/archive/id/draft-rampalli-pedigree-00.html); [AIP](https://www.ietf.org/archive/id/draft-singla-agent-identity-protocol-03.html) — complementary DID/capability models (**draft status**).

### 1.4 SaaS-embedded vs custom cloud agents

| Dimension | SaaS-embedded (e.g. Copilot Studio) | Custom cloud (Bedrock AgentCore / Vertex·Gemini Agent Platform / self-hosted) |
|-----------|--------------------------------------|--------------------------------------------------------------------------------|
| Control plane | Vendor-managed; tenant policies (DLP, ACP, Entra CA) | Enterprise-managed IaC + cloud IAM |
| Agent identity | Auto-provisioned Entra Agent ID; blueprint FIC; tenant cannot mint tokens as agent | Explicit agent principal (SPIFFE / workload identity); you own mint policies |
| Acts-as | Connector OBO + maker/user connections | You design JWT inbound + OBO/2LO outbound |
| PEP placement | Power Platform connector runtime, channel auth, Entra CA (channel-dependent) | Agent gateway, MCP gateway, API gateway, IdP token mint |
| Data residency / egress | Vendor regions + DLP; limited custom egress control | VPC/Service Controls, private endpoints, explicit egress allowlists |
| IGA | Agent IDs visible in Entra; migrate legacy app regs | Must enroll agents in SCIM/IGA registry yourself (CSA AIGF pattern) |

**Uncertainty:** Feature maturity varies (e.g. Google Agent Identity GA on Agent Runtime vs preview on some Gemini Enterprise surfaces — verify per product page at deployment time). Copilot Studio Entra Agent ID rollout referenced as **May 2026** for new agents; legacy app registrations persist until migration.

---

## 2. Tool / action plane → where the PEP sits

### 2.1 Plane taxonomy

| Plane | Mechanism | Identity signal typically available | PEP natural seat |
|-------|-----------|-------------------------------------|------------------|
| Native function calling | Model emits structured tool call; harness executes | Session user + agent config allowlist | **Harness** (before invoke); secondary at API |
| MCP | JSON-RPC tools/resources over HTTP (or STDIO) | OAuth 2.1 access token (HTTP); env creds (STDIO) | **MCP gateway** or MCP server; AuthZEN PDP |
| Browser / RPA | DOM automation, click paths | Often session cookies / RPA robot SA | Fragile — prefer not for regulated money movement; if used: robot identity + HITL + session isolation |
| Direct API SDKs | Agent code calls SDK with cloud IAM / API key | Workload identity or static secret | **API gateway** + cloud IAM; never embed long-lived keys in prompts/memory |

### 2.2 MCP authorization (transport) vs fine-grained AuthZ

**MCP Authorization (2025-11-25 spec):** OAuth 2.1 for HTTP transports; Protected Resource Metadata (RFC 9728); Auth Server Metadata (RFC 8414); Resource Indicators (RFC 8707); PKCE; **token audience binding mandatory**; **token passthrough forbidden**. STDIO should use environment credentials, not this flow. Source: [MCP Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) (spec date in URL path 2025-11-25).

**Gap:** OAuth scopes do not express "transfer $X from account A to B." Fine-grained AuthZ needs a PDP.

**COAZ-MCP / AuthZEN:** Maps MCP messages → AuthZEN Subject-Action-Resource-Context. PEP = MCP gateway or server; `subject.id` trust-anchored to token claim (`sub` or designated OBO claim); agent in `context`; fail-closed. Declared mappings via `x-authzen-mapping` in tool `inputSchema`. Source: [COAZ-MCP Draft 1](https://openid.github.io/authzen/authzen-coaz-mcp-binding-1_0.html) (OpenID; normative refs dated 2026). Related: [AuthZEN MCP profile discussion](https://github.com/openid/authzen/issues/429).

### 2.3 PEP placement cheat-sheet (finance-oriented)

| PEP seat | Strengths | Weaknesses | Prefer when… |
|----------|-----------|------------|--------------|
| Harness / agent runtime | First line; can block tool before network | Bypass if agent code escapes sandbox | Interactive + trusted runtime |
| MCP gateway | Centralize many servers; AuthZEN filter; no per-server code change | Must prevent direct server bypass | Multi-MCP enterprise |
| API gateway | Mature; WAF; existing API products | Blind to MCP/tool semantics unless enriched | Direct SDK / REST backends |
| IdP token mint | Audience, scope, lifetime, DPoP at issuance | Cannot see tool arguments | Always — necessary but not sufficient |
| Cloud IAM | Strong for cloud resource APIs | Coarse for SaaS business objects | Agent→cloud resource |
| Connector platform (Power Platform) | DLP/ACP revalidation | Channel gaps for CA on agent ID | M365 / Dynamics estates |

**AWS note:** Front AgentCore Runtime with **AgentCore Gateway**; restrict runtime invocation to gateway role / `allowedWorkloadConfiguration` so callers cannot bypass the PEP. Documented confused-deputy guidance on gateway execution role trust policy. Source: [runtime-oauth](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-oauth.html).

**Google note:** Agent Gateway + Identity-Aware Proxy pattern; Principal Access Boundary + VPC-SC for egress. Source: [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview).

---

## 3. Identity binding points (detail)

### 3.1 Runtime / workload AuthN

| Pattern | Binding | Notes |
|---------|---------|-------|
| SPIFFE / Google Agent Identity | `spiffe://…/resources/…/agent` → IAM principal | Per-agent; not shared SA; mTLS + optional DPoP; certs short-lived (Google: 24h auto-rotated) |
| Bedrock AgentCore workload identity | Auto-created with runtime; SLR manages tokens | Inbound: SigV4 **xor** JWT; outbound: credential providers |
| Entra Agent ID | Agent subtype SP; Microsoft blueprint FIC | Tenant admins **cannot** mint tokens as the agent; owner added as sponsor |
| Self-hosted | SPIRE selectors / K8s SA / cloud instance identity | Must still exchange for audience-bound access tokens (RFC 7523 / 8693) |

**Control:** Prefer attested identity → short-lived, audience-bound, PoP-constrained tokens. Avoid long-lived API keys in agent env or memory (secretless / federation pattern emphasized in 2026 vendor guidance, e.g. Akeyless/Aembit blogs — vendor-authored; treat as secondary).

### 3.2 Acts-as models

| Model | When used | Audit appearance | Risk if misapplied |
|-------|-----------|------------------|-------------------|
| **User OBO** | Interactive; user-owned SaaS data | Dual: user + agent | Token reuse across users/sessions; over-broad delegated scopes |
| **Agent-as-principal** | Batch; org-owned data; system automation | Agent (+ sponsor) | Standing privilege; shadow admin |
| **Shared service account** | Legacy RPA / "bot user" | Ambiguous | **Avoid** in regulated estates — breaks attribution and SoD |
| **Hybrid** | Agent identity for cloud APIs + OBO for user SaaS | Dual-path logging required | Inconsistent policy across paths |

**Google matrix** (own authority vs user-delegated) is a clean reference: 3LO for user-delegated external tools; Agent Identity / 2LO / API key for agent's own authority — with API keys vaulted in auth manager, not in agent code. Source: [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview).

**AWS:** Token vault binds OAuth tokens to **workload identity + user ID** from inbound JWT — good multi-user isolation pattern if inbound AuthN is JWT-verified. Source: [runtime-oauth](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-oauth.html).

### 3.3 Token lifetime and constraints

Minimum bar for finance:

- Short-lived access tokens; rotate refresh for public clients (OAuth 2.1).
- **Audience binding** (RFC 8707) — MCP requires clients send `resource`; servers validate `aud`.
- **Proof-of-possession** (DPoP RFC 9449 / mTLS) where platforms offer it — reduces replay of stolen bearer tokens.
- **Task / amount / account constraints** via RAR (RFC 9396) or AuthZEN resource properties — scopes alone insufficient for payments.
- Deny `GetWorkloadAccessTokenForUserId`-style unverified user binding in production (AWS explicit warning).

### 3.4 Memory / context isolation (multi-user hard case)

| Failure | Mechanism | Control |
|---------|-----------|---------|
| Cross-user memory bleed | Shared vector store / session cache keyed only by agent | Key by `tenant + user + session`; separate encryption keys |
| Credential reflection | Prior session's OAuth token or API key left in context window | Never put secrets in LLM context; vault at PEP; Google gateway decrypt pattern |
| Tool result leakage | Agent A's tool output cached into Agent B's prompt | Per-session ephemeral context; TTL wipe |
| Multi-tenant MCP | Single MCP connection spanning tenants | Per-tenant servers or strict PEP on arguments (ASI03 / Asana MCP cross-tenant class of bug — secondary reports) |

**Finance:** Cross-user bleed can violate client confidentiality and create insider-trading-adjacent data exposure. Treat memory stores as regulated data stores (access logs, residency, retention).

---

## 4. Trust & control topology

### 4.1 Enterprise-managed vs vendor-SaaS control plane

| Question | Enterprise-managed | Vendor-SaaS |
|----------|--------------------|-------------|
| Who creates agent identity? | Your IaC / IdP | Vendor auto-provisions (Entra Agent ID, AgentCore workload ID) |
| Who is PEP operator? | Your platform team | Shared: vendor runtime + your policies (DLP, CA) |
| Can you inspect every tool call? | Yes if you own gateway | Depends on product telemetry / SIEM export |
| Decommission | You must delete IAM bindings (Google: deleting agent does **not** auto-remove IAM bindings) | Entra: delete agent removes Agent ID — verify |
| Shadow agents | DIY scripts, personal API keys, unregistered Copilots | Same — plus marketplace agents |

### 4.2 Data residency / egress

- Bind egress to **agent principal** in VPC-SC / firewall / CASB, not only to human user.
- SaaS connectors: DLP policies may allow connectors that still exfiltrate via "allowed" SaaS → require allowlists of destinations and fields.
- Browser/RPA agents: highest egress uncertainty — prefer disable for material finance data.

### 4.3 HITL gates

Place HITL at the **authorization decision**, not only in the chat UX:

| Action class | Gate |
|--------------|------|
| Read non-sensitive | Auto with audit |
| Read sensitive / client data | Policy + optional step-up |
| Write / mutate | AuthZEN permit + maker ≠ checker where SoD applies |
| Payments / trading / irreversible | Dual control / CIBA / ticket; amount ceilings in token or PDP |
| Delegation to sub-agent with elevated tools | Explicit re-consent or policy allowlist of child capabilities |

OpenID CIBA appears in WIMSE credential-delegation draft as async consent mechanism for agents — relevant for scheduled jobs.

---

## 5. Failure modes (mapped to bindings)

### 5.1 Impersonation / confused deputy

| Variant | Binding failure | Control |
|---------|-----------------|---------|
| MCP token passthrough | Server forwards client token upstream | Forbidden by MCP auth spec; separate upstream token |
| Static client_id proxy | MCP proxy uses one client ID for many users | Per-user consent; dynamic clients; resource indicators |
| Unverified userId header | Caller supplies victim user id | Prefer JWT path; IAM deny on unverified APIs |
| Cross-agent trust | High-priv agent trusts internal low-priv agent | Per-hop AuthZ; attenuating delegation tokens |
| Declared mapping spoof | MCP server sets fake `subject.id` | COAZ: PEP verifies subject against token claim |

### 5.2 Over-broad tool scopes

- Connector granted `Operations.Execute.All` instead of per-operation scopes (Copilot Studio Entra Agent ID model).
- MCP client requests all `scopes_supported` (spec's default strategy for general clients) — mitigate with server-side least privilege and step-up.
- RPA robot runs as domain admin.

**Control:** Least privilege at mint + fine-grained PEP on arguments + periodic access reviews in IGA.

### 5.3 Sub-agent privilege inflation

- Parent passes its bearer token to child (SPIFFE JWT-SVID handoff without re-bind).
- RFC 8693 nested `act` used as if it **enforced** attenuation — it does not (IETF drafts + RFC 8693 §4.1).
- Delegation chain splicing (mismatched subject_token / actor_token) — see draft-sweeney (2026).

**Control:** New scoped token per hop; cryptographic parent commitment; PDP evaluates child capabilities ⊆ parent mandate ∩ org ceiling (PEDIGREE dual-enforcement idea).

### 5.4 Shadow agents outside IGA

- Personal ChatGPT/Claude with corporate API keys.
- Unregistered Bedrock agents with broad execution roles.
- Legacy Copilot app registrations not migrated / not sponsored.

**Control:** CSA **Agent Identity Governance Framework (AIGF)** pattern — registry of agent identity types, sponsoring principal chain, JIT privileges, SCIM lifecycle events to revoke. Source: [CSA AIGF v1](https://labs.cloudsecurityalliance.org/agentic/agentic-identity-governance-framework-v1/) (draft ~2026; confirm version in use). Pair with inventory of cloud agent principals and Entra Agent IDs.

---

## 6. Pattern → binding → control matrix (summary)

| Major pattern | Critical binding points | Controls that bind where | Finance / regulated notes |
|---------------|-------------------------|--------------------------|---------------------------|
| Interactive chat+tools | User session; OBO; harness/MCP PEP; session memory | IdP CA; OBO consent; AuthZEN on tools/call; per-user memory keys | HITL feasible; dual audit (user+agent); SoD on maker vs prod access |
| Long-running / scheduled | Workload identity; pre-consent vault; async HITL | Attested runtime; task-scoped tokens; CIBA/ticket; job TTL | Standing OBO refresh = high risk; material actions need dual control |
| Multi-agent orchestration | Per-node identity; hop attenuation; intent re-check | Distinct principals; delegation tokens; PDP per hop | Sub-agent inflation = SoD bypass; full provenance for audit |
| SaaS-embedded (Copilot Studio) | Entra Agent ID; connector OBO; channel auth | Entra visibility + ACP/DLP; CA where channel supports | Channel enforcement gaps; register all agents in IGA |
| Custom cloud (Bedrock/Vertex) | Workload/SPIFFE ID; gateway PEP; VPC egress | Gateway-only invoke; JWT inbound; vaulted outbound; VPC-SC | You own registry/decommission; deny unverified user binding |
| MCP tool plane | Audience-bound token; gateway PEP; no passthrough | MCP OAuth + COAZ-AuthZEN; fail-closed | Parameter-level AuthZ for transfers; avoid STDIO secrets in multi-user hosts |
| Browser/RPA plane | Robot identity; fragile session | Prefer eliminate; else HITL + dedicated identity + isolation | Generally poor fit for regulated money movement |

---

## 7. Practical binding checklist (for reviews)

1. Does every agent have a **unique, non-impersonable** principal (not a shared SA)?
2. Is **acts-as** explicit per tool path (OBO vs agent-as-principal), with dual logging?
3. Where is the **PEP** for each tool plane, and can it be bypassed?
4. Are tokens **audience-bound, short-lived, and PoP-constrained** where available?
5. Do sub-agents receive **attenuated** credentials (not parent bearer reuse)?
6. Is memory keyed and wiped per user/session?
7. Are material actions behind **HITL / dual control** with SoD?
8. Is the agent in **IGA** (owner/sponsor, capabilities, decommission triggers)?
9. Is egress constrained to the **agent principal**?
10. Are shadow paths (personal keys, direct runtime invoke past gateway) denied?

---

## 8. Sources

Primary / normative-leaning (retrieved or dated ~2025–2026):

1. Microsoft Learn — [Manage Entra Agent IDs](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-use-entra-agent-identities) (retrieved 2026-09-15)
2. Microsoft Learn — [App registration, agent identities, and authentication](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-certificates-configuration-values) (retrieved 2026-09-15)
3. Microsoft Learn — [OBO for custom connectors](https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-custom-connector-on-behalf-of) (retrieved 2026-09-15)
4. AWS Docs — [Bedrock AgentCore Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html) (retrieved 2026-09-15)
5. AWS Docs — [Inbound Auth and Outbound Auth](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-oauth.html) (retrieved 2026-09-15)
6. Google Cloud — [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview) (last updated 2026-09-14)
7. Google Cloud — [Use Agent Identity with Agent Runtime](https://docs.cloud.google.com/gemini-enterprise-agent-platform/scale/runtime/agent-identity) (retrieved 2026-09-15)
8. MCP Spec — [Authorization (2025-11-25)](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)
9. OpenID — [COAZ-MCP Binding Draft 1](https://openid.github.io/authzen/authzen-coaz-mcp-binding-1_0.html) (refs 2026)
10. OpenID AuthZEN — [Issue #429 MCP Profile](https://github.com/openid/authzen/issues/429)
11. OWASP — [Top 10 for Agentic Applications 2026](https://genai.owasp.org/download/52117) (~Dec 2025)
12. CSA Labs — [Agent Identity Governance Framework v1](https://labs.cloudsecurityalliance.org/agentic/agentic-identity-governance-framework-v1/)
13. IETF — [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/)
14. IETF — [draft-sweeney-wimse-credential-delegation-00](https://www.ietf.org/archive/id/draft-sweeney-wimse-credential-delegation-00.html)
15. IETF — [PEDIGREE draft-rampalli-pedigree-00](https://www.ietf.org/archive/id/draft-rampalli-pedigree-00.html)
16. IETF — [AIP draft-singla-agent-identity-protocol-03](https://www.ietf.org/archive/id/draft-singla-agent-identity-protocol-03.html)
17. RFC 8693 — OAuth 2.0 Token Exchange; RFC 8707 Resource Indicators; RFC 9449 DPoP; RFC 9396 RAR (normative building blocks)

Secondary / vendor narrative (use cautiously):

18. Aembit — [IAM for Agentic AI](https://aembit.io/blog/iam-agentic-ai/) (2026 blog)
19. Akeyless — [AI Agent Identity Security 2026 Deployment Guide](https://www.akeyless.io/blog/ai-agent-identity-security-2026-deployment-guide/)

---

## 9. Uncertainty log

| Item | Status |
|------|--------|
| COAZ-MCP, AIP, PEDIGREE, WIMSE delegation drafts | Implementers' drafts — design guidance, not settled standards |
| Copilot Studio CA on Agent ID | Documented as end-to-end mainly for Teams; other channels may differ |
| Google Agent Identity product GA/preview mix | Confirm per surface at deployment time |
| CSA AIGF | Framework maturity / adoption in finance still early |
| Exact OWASP ASI numbering / PDF contents | Cited from official download + secondary explainers; download PDF for audit evidence packs |
| "May 2026" Entra Agent ID cutoff | Per Microsoft Learn at retrieval; re-verify if planning migrations |

---

*End of 01-architecture-identity-binding.md*
