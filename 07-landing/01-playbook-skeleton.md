# Enterprise Landing Playbook Skeleton — Agentic Identity

**Pack:** Agentic Identity Enterprise Landing (finance-regulated aware)  
**Research date:** 2026-09-15  
**Nature:** Phased **outline only** — no code, no compose, no product scaffold.

Maps to binding points in `01-ai-architecture-patterns.md` and vendors in `02-vendor-matrix-tier1.md`.

---

## Phase 0 — Preconditions (week 0)

**Outcomes**
- Executive sponsor (CISO + App/AI platform + IAM).
- Risk appetite statement for autonomous vs attended agents.
- Decision: **system of record** for agent identities (typically enterprise IdP: Entra Agent ID or Okta for AI Agents).

**Exit criteria**
- Written scope: in-scope channels (M365 Copilot, Bedrock, custom K8s, RPA, etc.).
- Regulatory overlay list (e.g., SOX ITGC, GLBA, FFIEC CAT themes, internal model risk) — *map controls, don’t invent new law*.

---

## Phase 1 — Discover

**Goal:** See the real estate before issuing more credentials.

| Workstream | Activities | Artifacts |
| --- | --- | --- |
| **Inventory** | Enumerate SaaS-embedded agents, cloud agent runtimes, RPA bots, IDE/local agents, MCP servers | Agent inventory (owner, platform, data classes, auth method) |
| **Credential archaeology** | Find shared bot accounts, static API keys in repos/secret stores, long-lived OAuth grants | NHI/credential risk register |
| **Shadow agents** | DLP/CASB signals for credential paste; unsanctioned GPT/agent SaaS | Shadow-IT agent list |
| **Data classification touchpoints** | Which tools can read customer/PII/payment data? | Data-flow sketches (logical) |
| **Vendor discovery features** | Entra Agent Registry / Agent 365 views; Okta agent list; CyberArk/Idira AI agents inventory; AWS resource tagging | Consolidated CMDB/IGA feed design |

**Identity controls to bind now**
- Ownership/sponsor field mandatory for anything left running.
- Quarantine shared SA used by >1 agent.

**Finance notes**
- Treat orphan agents like orphan service accounts in ITGC.
- Preserve evidence of discovery methodology for audit.

**Exit criteria**
- ≥90% of known production agents have owner + platform + auth pattern tagged.  
- Top 10 privileged paths identified (payments, core banking APIs, customer master, etc.).

---

## Phase 2 — Pilot architecture

**Goal:** One attended + one light autonomous pattern, correctly bound — not a platform rewrite.

| Pilot | Suggested shape | Binding emphasis |
| --- | --- | --- |
| **Pilot A — Attended chat+tools** | Internal employee assistant with 3–5 low-risk tools | User OBO ∩ Agent-as-principal; no shared SA |
| **Pilot B — Bounded autonomous** | Scheduled summarizer or ticket triage **without** payment authority | Workload identity; tool allowlist; HITL for escalation |

**Architecture decisions (record in ADR)**
1. IdP of record for agent principals.  
2. Token pattern: XAA/ID-JAG vs classic OBO vs AWS AgentCore OBO.  
3. MCP strategy: allowlisted servers only; broker required for privileged MCP?  
4. Where PEPs live (IdP, gateway, cloud IAM, app).  
5. Logging schema: `user_id`, `agent_id`, `tool`, `resource`, `decision`, `correlation_id`.

**Explicit non-goals for pilot**
- Unbounded sub-agent spawn.  
- RPA against core banking UI.  
- User password vaulting into LLM context.

**Exit criteria**
- Pilot ADRs approved by IAM + InfoSec + business control owner.  
- Threat model covers confused deputy, overbroad scopes, prompt injection → tool misuse.  
- Successful dry-run of revoke (disable agent / revoke tokens) < 15 minutes.

---

## Phase 3 — Identity controls

**Goal:** Make the safe path the default path.

### 3.1 Agent-as-principal
- Register every production agent in IdP/registry (Entra Agent ID / Okta AI Agents / equivalent).
- Blueprint or policy template per *class* (HR FAQ vs Payments Ops).
- Human sponsor + secondary owner; leaver workflow tested.

### 3.2 Workload identity
- Cloud agents: dedicated IAM roles / managed identities / Pod Identity — **no** human role reuse.
- Permission boundaries + deny capability to assume human roles (SCP/policy).
- Short-lived credentials only; ban long-lived keys for new agents.

### 3.3 User OBO / enterprise-managed delegation
- Prefer admin-governed connections (XAA / access packages) over per-user consent sprawl for production finance apps.
- Propagate user context as signed claims; downstream PEP enforces — agent is orchestrator not gatekeeper (AWS AGENTSEC03 / AgentCore guidance).
- Dual check: agent entitled **and** user entitled.

### 3.4 Shared SA elimination
- Break-glass only; time-bound; PAM vaulted; monitored.
- Migration plan from legacy RPA bot users.

### 3.5 HITL design
- High-impact tools → step-up / maker-checker aligned with existing payment controls.
- Reduce low-value prompts to prevent consent fatigue (NIST 2026 guidance).
- Ban elicitation for secrets.

### 3.6 Detection & response
- Alert on: new agent registration, blueprint change, scope expansion, anomalous tool fan-out, failed auth bursts.
- Playbooks: suspend agent, revoke tokens, disable blueprint, rotate client credentials.

**Exit criteria**
- Control standards document approved.  
- Conditional Access / IdP policies enforced on pilot agents.  
- Audit samples reconstruct “who/what agent/what tool” for 10 sample actions.

---

## Phase 4 — IGA / PAM integration

**Goal:** Agents enter the same governance factory as humans and NHIs.

### IGA
| Control | Implementation sketch |
| --- | --- |
| Joiner | Agent creation request → access package / connection policy → sponsor approval |
| Mover | Sponsor change workflows; re-attest scopes on org change |
| Leaver | Sponsor exit → reassign or disable agents automatically |
| Access review | Cadence **faster than annual** for agents that gain tools quickly (AWS note on drift speed) |
| Role model | Separate catalogs: Agent blueprints vs human roles vs workload roles |

### PAM
| Control | Implementation sketch |
| --- | --- |
| Secrets | No secrets in prompts/repos; vault or cloud secret store |
| ZSP / JIT | Prefer CyberArk/Idira broker or equivalent for privileged DB/MCP |
| Session audit | Record tool sessions for privileged paths |
| Credential hygiene | Rotation SLAs; note vendors with recreate-only client secrets |

### Integration sequence (recommended)
1. Feed agent inventory into IGA/CMDB.  
2. Enforce creation via request workflow (block console cowboy creates in prod).  
3. Attach PAM to privileged tool paths before expanding tool catalogs.  
4. Align SIEM parsers for agent actor claims.

**Exit criteria**
- Production agent create path requires IGA approval.  
- Privileged MCP/DB path has ZSP or equivalent JIT.  
- Quarterly (or tighter) access review executed once with evidence pack.

---

## Phase 5 — Scale

**Goal:** Many agents without many incidents.

| Domain | Scaling practice |
| --- | --- |
| **Platform** | Golden paths / blueprints for chat+tools, batch, multi-agent; paved road CI checks for identity bindings |
| **Multi-agent** | Per-agent principals; hop attenuation; spawn quotas; kill-switch drills |
| **MCP ecosystem** | Private allowlist of servers; Enterprise-Managed Authorization where available; block open DCR abuse |
| **Multi-cloud** | Federation patterns documented; one SoR for agent identity metadata |
| **Local/dev agents** | Hardened harness/sandbox policy; forbid prod credentials on laptops |
| **Metrics** | % agents with unique ID; % with OBO vs static key; MTTRevoke; shadow-agent count; scope-creep tickets |
| **Exam readiness** | Narrative + evidence: inventory, ownership, least privilege, logging, HITL for material actions |

**Organization**
- Platform team owns paved road.  
- IAM owns agent directory & policies.  
- Business control owners own tool entitlements for material risk.  
- IR owns suspend/revoke playbooks.

**Exit criteria**
- Policy: “No production agent without Agent-as-principal + approved blueprint.”  
- Measurable reduction in shared SA and static API keys.  
- Tabletop: rogue/compromised agent scenario passed with risk committee.

---

## Cross-phase RACI (skeleton)

| Activity | IAM | CISO/InfoSec | AI Platform | App Owner | Audit |
| --- | --- | --- | --- | --- | --- |
| Inventory | C | A | R | C | I |
| Pilot ADR | C | A | R | C | I |
| IdP agent policy | R | A | C | I | I |
| Tool entitlement | C | A | C | R | I |
| PAM privileged path | C | A | C | C | I |
| Access reviews | R | A | C | R | C |

R=Responsible, A=Accountable, C=Consulted, I=Informed

---

## Suggested timeline (indicative, not prescription)

| Phase | Calendar (speculation) |
| --- | --- |
| Discover | 4–8 weeks |
| Pilot architecture | 6–12 weeks |
| Identity controls hardening | Concurrent with pilot → +4 weeks |
| IGA/PAM integration | 8–16 weeks (depends on existing IGA maturity) |
| Scale | Ongoing; gated by metrics |

*Timeline is speculative and depends on existing IAM maturity.*

---

## Anti-patterns checklist (print for steering committee)

- [ ] Users pasting passwords/API keys into agents  
- [ ] One service account for “all bots”  
- [ ] Agent assumes human’s full role  
- [ ] Trusting LLM output filtering as authorization  
- [ ] Unbounded MCP dynamic client registration  
- [ ] HITL on every trivial tool (fatigue)  
- [ ] No sponsor / no revoke path  
- [ ] Sub-agents inheriting parent god-tokens  
- [ ] Local agents with prod credentials  
- [ ] Skipping inventory because “we only have a POC”

---

## References (see also files 01 & 02)

- NIST NCCoE agent identity concept direction (2026) & NIST blog 2026-08-27  
- AWS AGENTSEC03 & AgentCore user-context blog 2026-08-19  
- Microsoft Entra Agent ID GA (Apr 2026)  
- Okta/Auth0 XAA & AI agent token exchange  
- CyberArk/Idira Secure AI Agents docs  

---

*End of 03-landing-playbook-skeleton.md*
