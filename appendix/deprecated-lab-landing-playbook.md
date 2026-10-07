# Enterprise Agentic Identity — Landing Playbook Skeleton

**Research date:** 2026-09-15  
**Audience:** Kenny (IAM + agentic AI; finance/finance-relevant)  
**Purpose:** Phases only — architecture → controls. No full prose playbook yet.  
**Companion:** `02-vendor-matrix-tier1.md`

---

## Phase 0 — Framing & outcomes

- Define “agent” for the org (autonomous job vs user-delegated copilot vs embedded SaaS agent).
- Success criteria: inventory completeness, binding coverage %, kill-switch RTO, audit reconstructability (user→agent→tool→resource), certification cadence.
- Non-goals: LLM safety/content filters (adjacent); full CIAM rebuild.
- Regulatory map: access logging, privileged access, third-party/NHI, model risk adjacency (pointers only).

---

## Phase 1 — Discover runtimes & shadow agents

- Enumerate build platforms: Copilot Studio / Foundry / Bedrock AgentCore / Lang* / n8n / custom K8s / vendor SaaS agents.
- Enumerate IdP/PAM signals: Entra agent identities & classic SPs; Okta AI agents / Agent SSO; CyberArk/Idira AI inventory; cloud IAM roles used by agent workloads.
- Browser/endpoint/SaaS discovery (Okta ISPM/SAM, SailPoint Agentic Fabric, or equivalent) for unsanctioned agents.
- Output: **agent inventory v0** with owner gaps and runtime tags.

---

## Phase 2 — Classify interaction patterns

For each agent (or pattern family), label:

| Pattern | Typical binding need |
|---------|----------------------|
| User-delegated (OBO) interactive | User `sub` + agent actor; short-lived downstream tokens |
| Autonomous / scheduled | Agent-as-principal / workload identity; no user session |
| Agent→agent | Nested `act` / ID-JAG chain; depth limits |
| Agent→MCP/tools | MCP client auth + tool PEP |
| Agent→privileged infra/DB | PAM/ZSP broker; no standing secrets |
| Agent→AWS APIs | Workload role ≠ data role; session tags / OBO |

- Flag **impersonation anti-patterns**: shared “god” service accounts, long-lived API keys in prompts/env, agent role = data plane admin.

---

## Phase 3 — Choose binding model(s)

- Select **primary IdP for agent identity** (often Entra or Okta Workforce) vs **dev/CIAM** (Auth0) vs **privilege broker** (CyberArk/Idira) vs **cloud workload** (AWS IAM).
- Decision rules (examples):
  - Microsoft-heavy + CA/Governance already mature → Entra Agent ID first-class.
  - XAA-capable SaaS/MCP + Okta SSO estate → Agent SSO / Okta for AI Agents.
  - Privileged MCP/DB → Idira Identity Broker + ZSP.
  - AWS-hosted tools → IRSA/Pod Identity/Roles Anywhere **plus** user-context pattern (AgentCore OBO / tagged STS).
- Document **dual-binding** where required (workload AuthN ∧ user delegation).
- Explicitly reject “IRSA alone = agent identity program.”

---

## Phase 4 — IdP / PAM / IGA controls

- **Lifecycle:** register → owner/sponsor → activate → certify → suspend/delete; leaver-driven sponsor transfer.
- **Credentials:** prefer federated/workload identity / pubkey; ban long-lived agent secrets in code.
- **AuthN policy:** Conditional Access / Okta policies for agent identities vs agent-user OBO paths.
- **AuthZ defaults:** least privilege packages (Entra access packages; Okta grants; IAM permission boundaries).
- **IGA:** access reviews / certifications for agent entitlements; SailPoint (or native IdP) as governance plane if used.
- **PAM:** vault or broker for privileged tools; zero standing privilege for break-glass paths.

---

## Phase 5 — MCP / tool policy enforcement point (PEP)

- Inventory MCP servers; require registration allowlist.
- Choose PEP placement: IdP token validation at MCP; Auth0 FGA/tool checks; CyberArk broker; custom gateway.
- Define grain: connection → tool → (future) argument constraints for high-risk tools.
- Separate **discovery** of tools from **execution** authorization.
- Standard: no passthrough-without-attribution in regulated paths.

---

## Phase 6 — Audit & SIEM

- Mandatory fields: initiating user (if any), agent ID, client/app ID, actor chain (`act`/ID-JAG), tool name, target resource, decision (allow/deny), correlation ID.
- Pipe IdP logs + broker/MCP logs + CloudTrail/cloud audit into SIEM with agent-specific detections (new agent, privilege spike, OBO to sensitive apps, kill-switch failures).
- Evidence pack for audit: inventory snapshot, certification results, revoke drills.

---

## Phase 7 — Human-in-the-loop (HITL)

- Risk tier tools/actions requiring approval (payments, entitlement change, external comms, prod writes).
- Bind approvals to **user + agent + action**, not agent alone.
- Break-glass procedure with time-bound elevation and enhanced logging.

---

## Phase 8 — Kill switch & incident response

- Object-scoped disable (single agent/blueprint).
- Tenant/org-scoped block (CA templates / Okta deactivate / broker suspend).
- Credential/session revocation TTLs.
- Tabletop: prompt-injection leading to tool abuse; stolen agent credential; shadow agent.
- Measure RTO/RPO for agent containment.

---

## Phase 9 — Pilot

- Pick 1–2 patterns (e.g., OBO research copilot + autonomous batch) in a low-blast ring.
- Implement binding end-to-end; prove audit reconstruct; run certify + kill-switch drill.
- Exit criteria before scale: owner 100%, no shared secrets, PEP on all tools, SIEM detections live.
- Scale plan: pattern→platform standards; exception register for EA/preview features (Auth0 Agent as Principal, XAA limits, Entra preview CA).

---

## Phase 10 — Operate & improve

- Quarterly inventory drift + certification.
- Track vendor maturity (GA promotions) against exception register.
- Architecture review board for new agent platforms.
- Feed lessons into control standards (binding model catalog, MCP PEP standard, AWS dual-identity pattern).

---

## Phase dependency sketch

```
0 Framing → 1 Discover → 2 Classify → 3 Binding model
                ↓                         ↓
         4 IdP/PAM/IGA  ←————————→  5 MCP/tool PEP
                ↓                         ↓
              6 Audit/SIEM ←——————→ 7 HITL
                ↓
         8 Kill switch/IR → 9 Pilot → 10 Operate
```

---

## Control mapping cheat-sheet (phase → binding points)

| Phase | Runtime AuthN | Acts-as | MCP/tool | AuthZ grain | Audit | Kill | IGA | Cloud WI |
|-------|---------------|---------|----------|-------------|-------|------|-----|----------|
| 1 Discover | inventory | — | inventory | — | baseline | — | owner gaps | role inventory |
| 2 Classify | tag | pattern | tag | target grain | — | — | — | workload vs app |
| 3 Bind | choose | choose | choose | choose | design | design | design | design |
| 4–5 Build | implement | OBO/auton | PEP | scopes→tools | emit | wire | lifecycle | IRSA/RA |
| 6–8 Assure | — | — | — | — | SIEM | drills | certify | CloudTrail |
| 9–10 Scale | standards | standards | standards | tighten | evidence | runbooks | cadence | multi-account |

