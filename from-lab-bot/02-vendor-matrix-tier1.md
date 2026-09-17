# Tier-1 Vendor Matrix: Agentic Identity Binding Points

**Research date:** 2026-09-15  
**Audience:** Kenny (Security Engineering — IAM + agentic AI; finance/CLSA-relevant)  
**Scope:** Deep-dives scored against identity binding points. Docs-only; primary sources preferred.  
**Status legend:** **GA** | **Preview** | **Announced** | **Doc-only** | **Unclear** (as of research date)

> **How to read maturity labels:** Prefer vendor “generally available” / “early access” / “public preview” statements in primary docs over marketing. Items marked **Marketing claim** are not treated as evidence of production controls.

---

## Executive snapshot (one-line GA posture)

| Vendor | One-line posture (2026-09-15) |
|--------|-------------------------------|
| **Microsoft Entra Agent ID** | **Platform GA (Apr 2026)**; first-class agent identities + OBO/autonomous CA, access packages, disable/CA kill paths; some admin UX and agent-user CA extensions still **Preview**. |
| **Okta + Auth0** | **Split stack:** Auth0 Auth for MCP + OBO **GA (May 2026)**; Agent as Principal **Early Access (by Jul 2026)**; XAA **Early Access**; Okta **Agent SSO GA (Aug 2026)** + **Okta for AI Agents GA (since May 2026)** for discovery/governance/kill switch. |
| **CyberArk / Idira Secure AI Agents** | **GA path:** Secure AI Agents targeted **GA ~Dec 2025**; continued as **Idira Secure AI Agents** with live product docs (discovery, Identity Broker, MCP, ZSP, suspend); authZ grain and audit fidelity **vary by MCP connection mode**. |
| **AWS workload identity + agentic guidance** | **Workload AuthN primitives are GA** (STS, IRSA, Roles Anywhere, EKS Pod Identity); **agent-as-principal IdP product is not**—AgentCore Identity / Well-Architected patterns are **GA guidance + platform features**, not a full IGA agent directory. |

---

## Comparison matrix (binding points)

| Binding point | Microsoft Entra Agent ID | Okta + Auth0 for AI Agents | CyberArk / Idira Secure AI Agents | AWS (STS / IRSA / Roles Anywhere + AgentCore guidance) |
|---------------|--------------------------|----------------------------|-----------------------------------|--------------------------------------------------------|
| **Runtime / workload AuthN** | **GA** — agent identity + blueprint credentials; Auth SDK sidecar; federation for non-MS runtimes ([Learn: What's new](https://learn.microsoft.com/en-us/entra/agent-id/whats-new-agent-id)) | **GA/EA mix** — Okta agent registration + pubkey credentials; Auth0 agent-linked clients; Agent as Principal EA ([Okta AI agents API](https://developer.okta.com/docs/api/secures-ai/ai-agents); [Auth0 blog Jul 2026](https://auth0.com/blog/agent-as-principal-purpose-built-identity-for-agents/)) | **GA (docs)** — register agent → identity + credentials; broker URL as MCP endpoint ([CyberArk docs](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm)) | **GA** — IAM roles via STS; IRSA / Pod Identity on EKS; Roles Anywhere for off-AWS X.509→temp creds; AgentCore auto-creates **workload identity** per agent ([AWS Security Blog 19 Aug 2026](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/)) |
| **Acts-as model (OBO / agent-as-principal / impersonation risks)** | **GA** — OBO (user subject, agent delegated) + autonomous (agent identity subject); CA scopes differ by pattern ([CA for Agents](https://learn.microsoft.com/en-us/entra/identity/conditional-access/agent-id)) | **GA + EA** — Auth0 OBO token exchange **GA**; Agent as Principal **EA** with `act` / `sub_profile`; XAA/ID-JAG for enterprise-managed OBO; **risk:** nesting depth limits; agent-as-OBO-subject not yet ([Auth0 tokens](https://auth0.com/docs/ai-agents-mcp/agents-as-principal/agent-identity-in-tokens)) | **Doc-only / partial** — user→agent attribution in audit; brokering is PEP more than OAuth OBO IdP; passthrough mode weakens identity binding ([Architecture](https://docs.cyberark.com/manage/latest/en/content/secureai/architecture.htm)) | **GA patterns** — RFC 8693 OBO via AgentCore Identity; `AssumeRoleWithWebIdentity` + session tags for user-scoped IAM; **risk:** agent execution role over-privilege if not separated from user-scoped assume ([AgentCore blog](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/)) |
| **MCP / tool auth support** | **GA (docs)** — secure MCP server with Entra tokens; agent acquires token with `resource=` ([Secure MCP with Entra](https://learn.microsoft.com/en-us/entra/agent-id/secure-mcp-server-with-entra-id)); platform cites MCP/A2A ([What's new Apr 2026](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new)) | **GA + EA** — Auth for MCP **GA**; XAA framed as MCP Enterprise-Managed Authorization; Okta Agent SSO for XAA-speaking agents ([Okta PR 24 Aug 2026](https://www.okta.com/en-gb/newsroom/press-releases/okta-brings-first-class-identity-to-ai-agents-with-agent-sso/); [Auth0 May 2026](https://www.okta.com/newsroom/articles/auth0-may-2026-product-innovations/)) | **GA (docs)** — Identity Broker is MCP PEP; catalog + custom MCP; ZSP for Secure Infrastructure Access MCP ([Intro](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm)) | **Doc-only / guidance** — MCP IAM scoping patterns referenced from AgentCore posts; not a native MCP IdP broker ([AgentCore blog “next steps”](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/)) |
| **AuthZ grain (API scope vs tool/arg)** | **Mostly API/resource + OAuth scopes/app roles** — CA at token issuance; resource validates claims; **tool/arg grain unclear / app-side** unless custom | **Scopes GA; tool grain via FGA** — FGA Permissions Index **Developer Preview** (May 2026); FGA for MCP tools documented ([Auth0 FGA MCP](https://auth0.com/ai/docs/mcp/get-started/secure-mcp-server-with-auth0-fga)); XAA policy is connection-level | **Mixed** — ZSP/least privilege for SIA MCP; other MCP servers “may use different access control”; broker validates tokens; **tool invocation audited**, native arg-level AuthZ **unclear** | **IAM resource/API + ABAC conditions GA**; Knowledge Base metadata filters are **application-layer** (explicit caveat); Salesforce sharing is downstream; **tool/arg not first-class IAM** |
| **Audit (user→agent→resource)** | **GA** — agent sign-in classification; `agentType` on audit; agentSignIn; OBO vs autonomous distinguishable via identity type ([Disable agents](https://learn.microsoft.com/en-us/entra/agent-id/disable-agent-identities)) | **GA/EA** — Agent as Principal logs agent ID in tenant logs (EA); Okta System Log + agent lifecycle; XAA preserves user in ID-JAG path | **GA (docs)** — records human initiator, agent identity, tools, MCP target; fidelity drops in **passthrough** mode ([Architecture](https://docs.cyberark.com/manage/latest/en/content/secureai/architecture.htm)) | **Partial GA** — CloudTrail for STS/IAM; AgentCore binds user+agent in workload access token; end-to-end chain requires correlating Cognito/IdP + AgentCore + downstream — **no single agent IGA audit plane** |
| **Kill switch / revoke** | **GA** — disable agent identity/blueprint; CA block templates (agent identity, agent user, users→agents); revoke creation perms ([Disable](https://learn.microsoft.com/en-us/entra/agent-id/disable-agent-identities)) | **GA** — Okta for AI Agents: deactivation / kill switch (PR); Auth0 delete agent record (EA lifecycle); token revoke standard OAuth | **GA (docs)** — lifecycle: Suspended / Delete; admin actions audited ([Intro](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm)) | **GA primitives** — disable IAM role / revoke sessions / short TTL (e.g. 900s tagged creds); AgentCore Identity holds secrets so agent never stores them; **org-wide agent kill switch is DIY** |
| **IGA hooks (lifecycle, owner/sponsor, certification)** | **GA** — owners/sponsors/managers; access packages; sponsor Lifecycle Workflows **GA (May 2026)** ([What's new](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new)); Agent 365 registry consolidation | **GA** — Okta for AI Agents: owners, certifications, approvals, discovery of shadow agents (May 2026+); Agent SSO registers XAA agents in UD | **GA (docs)** — inventory ownership; register/secure selected agents; NHI adjacency via Idira platform | **Weak / external** — tags, owners in org process; no native access certification for “agents”; pair with SailPoint/IGA |
| **Cloud workload identity integration** | **GA docs** — configure non-MS agents (AWS Bedrock, n8n, GCP patterns); federation/sidecar ([What's new Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/whats-new-agent-id)) | **Partial** — Okta Workload / agent principals; Auth0 is app IdP more than cloud IW; XAA bridges enterprise IdP↔apps | **Discovery** of Bedrock/AgentCore/Copilot Studio agents; runtime binding is broker/MCP-centric, not IRSA replacement | **Native strength** — this *is* the cloud workload layer; Roles Anywhere for hybrid; Pod Identity/IRSA for K8s |
| **Regulated / finance fit (honest limits)** | Strong Entra estate fit (audit, CA, governance SKUs). **Limits:** Agent 365 registry API migration; mixed classic SP vs Agent ID; license (CA P1+, Governance); multi-cloud agents need federation discipline | Strong for SaaS/MCP + workforce IdP. **Limits:** Auth0 vs Okta product split; Agent as Principal / XAA still EA; ID-JAG depends on app support; marketing “first enterprise-grade MCP” — verify against Entra/CyberArk | Strong for **privileged** MCP/DB/infra + ZSP. **Limits:** not a universal IdP; AuthZ depends on MCP mode; PANW/Idira rebrand — contract/SKU clarity; discovery≠enforcement until registered | Excellent for **AWS resource** least privilege + OBO patterns. **Limits:** not enterprise agent directory/IGA; KB metadata filter not IAM-enforced; multi-cloud/SaaS needs AgentCore Identity or external IdP |

---

## 1. Microsoft Entra Agent ID

### Maturity (platform)

| Capability | Status | Evidence (dated) |
|------------|--------|------------------|
| Agent ID platform (identity + authZ framework, OAuth/MCP/A2A) | **GA** | Learn “What's new” — **April 2026**: “General Availability - Microsoft Entra Agent ID platform” ([link](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new)) |
| Admin wizard: create blueprint / agent identity | **Preview** | Agent ID What's new ([link](https://learn.microsoft.com/en-us/entra/agent-id/whats-new-agent-id)) |
| Access packages for agents; sponsor Lifecycle Workflows | **GA** | Access packages docs; sponsor LW **GA May 2026** ([What's new](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new)) |
| Conditional Access for agents (OBO + autonomous templates) | **GA** core; **Preview** extensions for agent *user accounts* (Jun 2026: CSA targeting, agent risk, compliant devices) | [CA for Agents](https://learn.microsoft.com/en-us/entra/identity/conditional-access/agent-id); [What's new Jun 2026](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new) |
| Disable / CA kill switch | **GA** | [Disable agent identities](https://learn.microsoft.com/en-us/entra/agent-id/disable-agent-identities) |
| Secure MCP server with Entra | **Doc-only / GA protocols** (implementation guide) | [Secure MCP](https://learn.microsoft.com/en-us/entra/agent-id/secure-mcp-server-with-entra-id) |
| Agent registry → Agent 365 consolidation | **Plan for change** (registry blades retired **1 May 2026**; Graph API re-reg) | [What's new Mar 2026](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new) |

### Binding-point notes

- **Runtime AuthN:** Blueprints + agent identities; Microsoft product agents (Foundry, Copilot Studio, Security Copilot) can auto-provision; third-party via Agent 365 CLI/SDK / sidecar / federation (Tech Community / Learn).
- **Acts-as:** Explicit dual model — OBO (user identity + delegated perms; CA on token exchange) vs autonomous (token to agent identity). Impersonation risk mitigated if CA + least-privilege grants; classic service principals tagged as agents lack Agent ID controls ([Disable doc](https://learn.microsoft.com/en-us/entra/agent-id/disable-agent-identities)).
- **MCP:** Treat MCP server as Entra resource; agent presents Bearer token; validate aud/iss/scopes. Grain remains OAuth scope / app role unless MCP server adds tool-level checks.
- **IGA:** Owners, sponsors, managers; access packages time-bound; sponsor transfer on leaver — strong finance ownership story. SailPoint adjacency: Entra is often the system of record *or* provisioned into IGA; no primary-doc SailPoint coupling found in this pass.
- **Finance fit:** Aligns with existing Entra CA + Governance programs; watch **Agent 365 API re-registration** and dual inventory (Entra identity vs M365 agent control plane).

### Marketing vs evidence

- Early 2025 “Zero Trust for agentic workforce” blogs = **announcement/preview era**; treat **Apr 2026 GA** Learn note as authoritative for platform status.

---

## 2. Okta + Auth0 for AI Agents

Treat as **two products with a shared protocol story (XAA / ID-JAG)**:

| Layer | Role |
|-------|------|
| **Auth0** | Developer IdP: Auth for MCP, OBO exchange, Token Vault, Agent as Principal, FGA |
| **Okta Workforce** | Enterprise SSO/governance: Agent SSO (XAA in core SSO), Okta for AI Agents (discovery, owners, certs, kill switch), ID-JAG issuance |

### Maturity

| Capability | Status | Evidence |
|------------|--------|----------|
| Auth0 Auth for MCP | **GA** | Okta newsroom **21 May 2026**; Auth0 blog “Auth for MCP Is Now Generally Available” ([newsroom](https://www.okta.com/newsroom/articles/auth0-may-2026-product-innovations/), [Auth0 blog](https://auth0.com/blog/auth0-auth-for-mcp-servers-generally-available/)) |
| Auth0 On-Behalf-Of Token Exchange | **GA** | Same May 2026 announcement |
| Auth0 Agent as Principal | **Early Access** (announced as Dev Preview early June; blog **30 Jul 2026** says EA) | [Auth0 blog](https://auth0.com/blog/agent-as-principal-purpose-built-identity-for-agents/); May 2026 note had forward-looking disclaimer |
| Auth0 FGA Permissions Index | **Developer Preview** (May 2026) | [May 2026 newsroom](https://www.okta.com/newsroom/articles/auth0-may-2026-product-innovations/) |
| Auth0 Cross App Access (XAA) / ID-JAG | **Early Access** (documented limitations) | [Auth0 XAA docs](https://auth0.com/docs/ai-agents-mcp/cross-app-access) |
| Okta Agent SSO (XAA in core SSO) | **GA** | Press **24 Aug 2026** ([PR](https://www.okta.com/en-gb/newsroom/press-releases/okta-brings-first-class-identity-to-ai-agents-with-agent-sso/)) |
| Okta for AI Agents (discover/govern/certify/deactivate) | **GA since May 2026** (per Aug PR) | Same PR; [developer AI agents](https://developer.okta.com/docs/api/secures-ai/ai-agents) |
| Okta AI agent token exchange (ID-JAG) | **GA docs** | [Set up AI agent token exchange](https://developer.okta.com/docs/guides/ai-agent-token-exchange/main/) |

### Binding-point notes

- **Runtime AuthN:** Register AI agents; public-key credentials; lifecycle STAGED→ACTIVE→INACTIVE→DELETED ([Okta API](https://developer.okta.com/docs/api/secures-ai/ai-agents)).
- **Acts-as:**  
  - **OBO:** user remains `sub`; agent in `act` (Auth0); nested `act` up to depth limits; **agent/client as OBO subject not supported yet** ([tokens doc](https://auth0.com/docs/ai-agents-mcp/agents-as-principal/agent-identity-in-tokens)).  
  - **Agent as Principal:** autonomous CC grant with agent `sub` + `sub_profile=ai_agent` (**EA**).  
  - **XAA/ID-JAG:** IdP policy gates cross-app/agent access without user consent prompts; MCP Enterprise-Managed Authorization extension ([XAA](https://auth0.com/docs/ai-agents-mcp/cross-app-access), [Okta XAA concept](https://developer.okta.com/docs/concepts/xaa/)).
- **MCP:** Auth for MCP (CIMD registration, resource-scoped tokens) **GA**; Agent SSO answers “XAA agents → apps/MCP”; Okta for AI Agents covers non-XAA agents (**marketing table** in PR — validate licensing).
- **AuthZ grain:** OAuth scopes + optional FGA for tools; connection policy for XAA — not universal arg-level PEP.
- **IGA:** Okta for AI Agents — owners, access certifications, approvals, shadow discovery (ISPM/SAM training paths). SailPoint adjacency: complementary IGA; not replaced by Okta.
- **Finance fit:** Good for broker-dealer SaaS tool access + MCP; require **EA feature flags** clarity (Agent as Principal, XAA) before risk acceptance; split Auth0 (CIAM/dev) vs Okta (workforce) ownership in CLSA-like orgs.

### Marketing flags

- “First enterprise-grade identity platform to authenticate and authorize MCP clients” (**May 2026 CPO quote**) — competitive claim; Entra/CyberArk also document MCP auth. Treat as **marketing**.
- Forward-looking asterisk on May 2026 Agent as Principal / Token Vault dates — **not commitments**.

---

## 3. CyberArk Secure AI Agents / Idira (NHI angle)

### Maturity

| Capability | Status | Evidence |
|------------|--------|----------|
| CyberArk Secure AI Agents solution | **GA targeted end Dec 2025** (vendor announcements) | [CyberArk product insights](https://www.cyberark.com/product-insights/cyberark-secure-ai-agents-a-closer-look-at-new-solution-capabilities/); ITWeb GA footnote |
| Idira platform + Secure AI Agents branding | **GA May 2026** (PANW Idira launch; agentic as add-on by SKU) | [PANW IR 12 May 2026](https://investors.paloaltonetworks.com/news-releases/news-release-details/palo-alto-networks-introduces-idira-next-generation-identity) |
| Product docs: discovery, register, Identity Broker, MCP, audit, suspend | **Doc-only confirmation of shipped capability** (assume GA if docs in “latest”) | [Intro](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm), [Architecture](https://docs.cyberark.com/manage/latest/en/content/secureai/architecture.htm) |

> **Unclear without tenant SKU check:** which Secure AI Agents features are included vs “Agentic and Machine identity” add-on under Idira packaging.

### Binding-point notes

- **Runtime AuthN:** Register AI agent → CyberArk/Idira identity + credentials; agent talks only to broker URL `https://…/mcp/<id>` (real MCP URL hidden).
- **Acts-as / impersonation:** Designed as **identity broker + privilege control**, not full OAuth IdP replacement. Strong story for **zero standing privilege** on Secure Infrastructure Access MCP. Passthrough mode: agent brings own token → **weaker agent/user identification**.
- **MCP / tool auth:** Primary PEP is the Identity Broker (session init, tool discovery, tool execution audited). Connection modes: OAuth app in Idira (full audit), Passthrough (limited audit), None (Idira as AS).
- **AuthZ grain:** Least privilege / ZSP for SIA-backed MCP; otherwise depends on remote MCP. Tool-level allow lists implied by registration; **arg-level AuthZ not clearly documented**.
- **Audit:** Explicit **human user → AI agent identity → tools → MCP/resource** — strong for regulated reconstruction *when* not using passthrough.
- **Kill switch:** Suspended / Delete lifecycle states.
- **IGA / NHI:** Inventory + ownership; sits in PAM/NHI platform (secrets, privilege). SailPoint adjacency: discovery/governance peer; CyberArk = privilege runtime.
- **Finance fit:** Best for agents that need **standing-privilege replacement** to databases, infra, privileged SaaS. Pair with Entra/Okta for workforce OBO into SaaS; don’t expect CyberArk alone to be the corporate IdP for all agent tokens.

### Marketing flags

- “First identity security solution purpose-built to protect AI agents with privilege controls” — **vendor positioning**; evaluate against Entra Agent ID + Okta for AI Agents on non-privileged API use cases.

---

## 4. AWS workload identity + agentic security guidance

### Framing for Kenny

AWS answers **“how does the runtime prove it may call AWS APIs?”** and increasingly **“how does user context ride into those calls?”** It does **not** (yet) replace an enterprise **Agent ID directory + IGA** the way Entra/Okta do.

| Layer | What it is | Agent identity relation |
|-------|------------|-------------------------|
| **Workload identity** | IAM role session via STS; IRSA / EKS Pod Identity; IAM Roles Anywhere (X.509) | Authenticates the **agent process / pod / host** — akin to machine identity |
| **App / user delegation** | Cognito/OIDC → inbound JWT; AgentCore Identity workload access token; RFC 8693 OBO; session-tagged AssumeRoleWithWebIdentity | Binds **user (or dept claim)** into downstream authZ — distinct from workload role |

### Maturity

| Capability | Status | Evidence |
|------------|--------|----------|
| STS temporary credentials | **GA** | AWS IAM/STS docs |
| IRSA (EKS) | **GA** | [EKS IRSA](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) |
| EKS Pod Identity | **GA** (alternative to IRSA) | AWS EKS docs / community decision guides |
| IAM Roles Anywhere | **GA** | [AWS Security Blog](https://aws.amazon.com/blogs/security/extend-aws-iam-roles-to-workloads-outside-of-aws-with-iam-roles-anywhere/) |
| Bedrock AgentCore Runtime workload identity + Identity OBO / token vault patterns | **GA platform features** (documented how-to **19 Aug 2026**) | [Propagate user authorization context…](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/) |
| Well-Architected Agentic AI Lens AGENTSEC03 | **Doc-only / guidance** | Referenced in same blog |
| Enterprise agent IGA (sponsors, cert campaigns, org-wide kill) | **Unclear / not native** | Use org IdP + IGA |

### Binding-point notes

- **Runtime AuthN:** One IAM role (or Roles Anywhere profile) per workload; short-lived creds; avoid long-lived keys.
- **Acts-as:** Recommended pattern — agent execution role **cannot** read data stores directly; only `AssumeRoleWithWebIdentity` / AgentCore OBO. User context via JWT claims → session tags / downstream sharing rules. **Impersonation risk** if agent role is broad or ID token forwarding is lax (`aud` checks matter).
- **MCP:** Guidance exists (“Secure AI agent access patterns… using MCP”); enforcement is IAM + app — **no CyberArk-style MCP broker** built-in.
- **AuthZ grain:** IAM actions/resources/conditions; DynamoDB leading keys example is solid ABAC; Bedrock KB metadata filter is **not** IAM-enforced (AWS states this caveat).
- **Audit:** CloudTrail + IdP logs; reconstruct user→agent→resource with correlation IDs — design required.
- **Kill switch:** Deny IAM, rotate trust, revoke OIDC client, disable AgentCore runtime — operational runbook, not a single “Disable agent” directory button.
- **Finance fit:** Correct foundation for AWS-hosted trading/research agents’ **cloud API** access; still need Entra/Okta (or Cognito+enterprise federation) for **workforce identity**, and PAM/IGA for privilege & certification. CLSA multi-cloud: Roles Anywhere or external IdP federation into AWS.

---

## Optional Tier-2 contrast (short)

### Google Agent Identity

Google Cloud documents **Agent Identity** as a **SPIFFE-based**, per-agent cryptographic principal (not shared SA; no long-lived SA keys; mTLS/DPoP binding), with auth manager for API keys / 2LO / 3LO OAuth, IAM + Principal Access Boundary, and MCP/gateway alignment. Supports agent-own vs user-delegated authority. Docs **last updated 2026-09-14**. Strong **GCP runtime** play; VPC-SC agent identity in ingress/egress noted; org IGA still external. Source: [Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview).

### SailPoint (Agentic Fabric / NHI governance)

SailPoint **Agentic Fabric** (Identity Security Cloud) emphasizes **discovery, unified NHI/agent registry, ownership, lifecycle, certifications, audit-ready reporting**, and datasets spanning agents, MCP servers, credentials, IAM roles — with **planned/runtime policy** direction for tool calls. Best read as **IGA adjacency** to Entra/Okta/CyberArk runtimes, not a replacement AuthN broker. Sources: [SailPoint docs](https://documentation.sailpoint.com/saas/help/agentic_fabric/index.html); product pages.

---

## Biggest evidence gaps / blockers (for CLSA-style landing)

1. **Cross-vendor “tool/arg” AuthZ** — only Auth0 FGA (and Google UAP “coming soon” messaging) approach tool grain; Entra/AWS/CyberArk mostly scope/resource/ZSP. Finance need: deny `transfer(amount>` …) style — expect **custom PEP**.
2. **Okta/Auth0 EA surface** — Agent as Principal + XAA limitations (1:1 org/connection, user must pre-exist, rate limits) block “all agents GA” narratives.
3. **CyberArk connection-mode audit cliff** — passthrough undermines user→agent chain; standards needed in architecture standards.
4. **AWS ≠ agent IGA** — easy to conflate IRSA with “agent identity program”; needs explicit binding model choice (see playbook skeleton).
5. **Entra Agent 365 registry migration** — inventory/control-plane dual-run and Graph API re-registration risk.
6. **SKU / packaging opacity** — Idira add-ons; Okta for AI Agents vs Agent SSO; Entra Governance/P1 for CA kill + access packages.
7. **Primary-doc silence on SailPoint connectors** for Entra Agent ID / Okta agents — adjacency assumed, not verified here.

---

## Citation index (primary)

| Date | Source |
|------|--------|
| 2026-04 | [Entra What's new — Agent ID platform GA](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new) |
| 2026-05-21 | [Auth0 for AI Agents May innovations](https://www.okta.com/newsroom/articles/auth0-may-2026-product-innovations/) |
| 2026-05 / ongoing | [Entra Agent ID What's new](https://learn.microsoft.com/en-us/entra/agent-id/whats-new-agent-id) |
| 2026-05-12 | [PANW Idira launch](https://investors.paloaltonetworks.com/news-releases/news-release-details/palo-alto-networks-introduces-idira-next-generation-identity) |
| 2026-07-30 | [Auth0 Agent as Principal EA blog](https://auth0.com/blog/agent-as-principal-purpose-built-identity-for-agents/) |
| 2026-08-19 | [AWS AgentCore user context propagation](https://aws.amazon.com/blogs/security/propagate-user-authorization-context-in-ai-agents-with-amazon-bedrock-agentcore/) |
| 2026-08-24 | [Okta Agent SSO GA PR](https://www.okta.com/en-gb/newsroom/press-releases/okta-brings-first-class-identity-to-ai-agents-with-agent-sso/) |
| 2026-09-14 | [Google Agent Identity overview](https://docs.cloud.google.com/iam/docs/agent-identity-overview) |
| undated “latest” | [CyberArk Secure AI agents intro/architecture](https://docs.cyberark.com/manage/latest/en/content/secureai/introduction.htm) |
| undated | [Auth0 XAA](https://auth0.com/docs/ai-agents-mcp/cross-app-access); [Auth0 agent tokens](https://auth0.com/docs/ai-agents-mcp/agents-as-principal/agent-identity-in-tokens); [Entra disable agents](https://learn.microsoft.com/en-us/entra/agent-id/disable-agent-identities); [Entra secure MCP](https://learn.microsoft.com/en-us/entra/agent-id/secure-mcp-server-with-entra-id) |
