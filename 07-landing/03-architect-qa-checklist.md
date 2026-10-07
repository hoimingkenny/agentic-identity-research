# Architect Q&A checklist — Agentic Identity + SailPoint Solution Architect

**Purpose:** After reading the research pack, what you should **be able to answer**, and what you should **ask**.  
**As of:** 2026-09-18  

---

## How to use this
- **Must answer** = your credibility as architect (design judgment, control gaps, SailPoint fit).  
- **Must ask** = discovery questions that keep the design honest (you don’t invent estate facts).  
- Dual lens: **IGA/SailPoint** (lifecycle, ownership, certs, SoD) + **agentic identity** (principal, delegation, PEP, revoke).

---

## A. What you should be able to answer

### A1. Problem & motivation
1. Why do AI agents need governance beyond “AI safety” and DLP?  
2. Why investment-bank / regulated accountability cares (attribution, SoD, books & records, walls)?  
3. Why **human IAM + JML** alone is necessary but not sufficient?  
4. Why **shared service accounts** fail for agents (attribution, blast radius, kill switch)?  
5. Why **“just run as the user”** (impersonation) is not a complete agent control model?  
6. Why **cloud workload identity alone** (IRSA/roles) is not the same as agentic identity?  
7. What is the **accountability gap** in one minute: who / on whose behalf / which agent / which tool / can we revoke?

### A2. Architecture literacy (agents)
8. What major **agent types** exist in enterprises (M365 Copilot, Studio agents, coding agents, custom LLM apps, RPA+AI, shadow)?  
9. For each type: where credentials usually live and what typically breaks?  
10. Where can a **PEP** sit (IdP, MCP gateway, API gateway, harness, PAM broker) — and why the **model is not a PEP**?  
11. Difference: interactive OBO vs long-running autonomous vs multi-agent hops.  
12. What “confused deputy,” standing secrets, and sub-agent privilege inflation mean in control language.

### A3. Agentic identity control model
13. The four binding points: workload identity, user OBO/delegation, agent-as-principal, ban shared SA.  
14. Effective privilege as **intersection**: user ∩ agent ∩ task (not union).  
15. What “good” requires for material actions: unique agent ID, owner, short-lived creds, external PEP, audit chain, selective kill.  
16. What agentic identity does **not** replace (PAM, DLP, cloud IAM, model safety — compose, don’t crown).

### A4. SailPoint / IGA architect answers
17. Where SailPoint sits vs IdP vs PAM vs secrets vs MCP gateway in an agent landing architecture.  
18. What SailPoint is strong at for this domain: **inventory of identities**, **ownership**, **JML-like lifecycle**, **access requests/certs**, **SoD policy**, correlating NHIs — and what it is **not** (runtime tool PEP, LLM gateway).  
19. How an **agent** should appear in IGA: identity object (or NHI), application/source, entitlements (roles/tools/scopes as modeled), owner/sponsor, leaver → disable.  
20. How human JML should **cascade** to agents the human owns/sponsors.  
21. Difference between certifying “user has App X” vs certifying “agent Y may call tool Z / hold connector A.”  
22. How to talk SailPoint ↔ Entra Agent ID / Okta agent registry / CyberArk privileged path without claiming SailPoint replaces them.  
23. A phased landing: discover → pilot identity binding → IGA hooks → privileged path → scale (from whitepaper outline).

### A5. Risk & program answers
24. How you risk-tier agents (read-only FAQ vs write/finance/privileged).  
25. What HITL is for (material actions) and why consent spam is a failed control.  
26. What success metrics look like: % agents with owner, % static keys eliminated, MTTRevoke, coverage of high-risk tools behind PEP.

---

## B. What you should ask (discovery)

### B1. Estate & inventory
1. What agent platforms are **approved** today (M365 Copilot, Copilot Studio, GitHub Copilot, Bedrock/Foundry, internal LLM apps)?  
2. What is **shadow** usage (personal ChatGPT, IDE agents, unregistered studio bots)?  
3. Is there any **inventory** of bots/NHIs/agents today? System of record?  
4. Who thinks they “own AI” — CISO, IAM, CTO, innovation, business units?

### B2. Identity & access today
5. IdP of record (Entra / Okta / other)? Any **Agent ID / agent registry** features in use or planned?  
6. How are **service accounts** owned, vaulted, reviewed, and revoked today?  
7. For Copilot/connectors: OBO vs app-permissions vs shared connectors — which patterns exist?  
8. Where do custom LLM apps get credentials (`.env`, vault, cloud roles, user tokens)?  
9. Is there an **MCP** or tool gateway in play, or tools called direct from runtimes?

### B3. SailPoint-specific
10. Current SailPoint scope: HR JML, apps onboarded, NHI/SA modeling maturity, AI/agent connectors if any?  
11. Can SailPoint already represent **non-human** identities cleanly (sources, owners, cert campaigns)?  
12. What would “agent” be in your data model — identity type, application account, CI in CMDB + linked identity?  
13. Leaver process today: disable AD/Entra — does anything disable **owned bots/agents**?  
14. SoD: any rules that must apply when an agent acts (e.g. initiate vs approve)?  
15. Integration paths: SailPoint ↔ Entra/Okta ↔ CyberArk/PAM ↔ secrets → who is system of record for what?

### B4. Risk, regulatory, ops
16. Which agent use cases are **material** (payments adjacency, client data, trading ops, entitlements, prod change)?  
17. What must HITL / maker-checker cover for AI-initiated actions?  
18. Audit requirements: fields IR/regulators expect (user, agent, tool, resource, decision)?  
19. Kill-switch expectation: minutes vs hours; who can press it?  
20. Cloud strategy: AWS/Azure/GCP — workload identity standards already?

### B5. Program & politics
21. Is the whitepaper meant to unlock budget, policy, or architecture mandate?  
22. Biggest fear of leadership — shadow AI, coding agents, Copilot oversharing, or unowned automations?  
23. What must **not** break (SailPoint JML, PAM, existing SA vaulting)?  
24. 90-day success: inventory + policy, or a technical pilot?

---

## C. One-page “architect oral exam” (practice aloud)

**Explain (5 minutes):**  
“Humans and service accounts are governed; agents are tool-using actors that fall through both. Shared SAs and user impersonation cannot give attribution + least privilege + revoke. Agentic identity adds agent principals and delegation; SailPoint extends IGA lifecycle/ownership/certs; IdP/PAM/gateways enforce runtime. We compose them.”

**Then invite:**  
“Before design, I need inventory of agent types, IdP/PAM/SailPoint boundaries, and which use cases are material.”

---

## D. Red flags if you cannot answer yet
- Equating Copilot with “just another app assignment in SailPoint” with no runtime binding story.  
- Saying SailPoint alone “solves agentic identity.”  
- Saying Entra/Okta Agent ID alone “solves IGA/JML for agents.”  
- Ignoring coding agents and homemade LLM apps because only M365 is in the AI committee deck.

---

## Related pack
`00a` problems · `00b` landscape · `00c` why identity · `04` whitepaper outline
