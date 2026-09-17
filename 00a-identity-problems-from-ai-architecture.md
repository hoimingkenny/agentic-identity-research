# Identity problems that arise from AI agents and their architectures

**Focus:** Problem identification only — not vendor solutions.  
**As of:** 2026-09-17  
**Audience:** Finance/enterprise-applicable research

---

## Core thesis

AI agents break classical IAM assumptions because **one “agent action” is not one identity**. A single tool call often mixes:

1. a **runtime/workload** (the process),
2. an **agent persona** (which agent),
3. a **human authority** (on whose behalf),
4. a **tool/resource** (what is being done),
5. sometimes **sub-agents** (who was delegated next).

Most enterprise pain is not “we need another SSO product.” It is that architectures create **new principals, new credential surfaces, and new accountability gaps** faster than identity systems were designed for.

---

## A. Root architectural causes (why identity breaks)

| Architectural reality | Identity problem it creates |
|---|---|
| LLM chooses tools; runtime executes them | Authorization often lives in the **prompt/harness**, not a real PEP |
| Tools need credentials to call APIs/DBs | Credentials get stuffed into env, memory, MCP config → **standing secrets** |
| Agent “helps the user” | Easy default = **impersonation / shared user token** → confused deputy |
| Agents run unattended (cron, queues) | No human session → **who is the subject?** Pre-consented refresh tokens linger |
| Multi-agent planners spawn workers | **Privilege inflation** across hops; audit chain breaks |
| SaaS Copilots + local IDE agents + cloud agents coexist | **Shadow agents** outside IGA/inventory |
| Same bot account used by many automations | **Shared service account** — attribution dies |
| Memory/context shared across users or sessions | Wrong user’s data/authority leaks into next task |

---

## B. Problem catalog (identity issues)

### 1. Missing or weak agent identity
Agents act without a first-class principal (no unique ID, owner/sponsor, lifecycle).  
**Symptom:** Logs show “system” or a shared app registration; you cannot disable *one* agent.

### 2. Acts-as ambiguity (delegation vs impersonation)
Unclear whether the call is:
- user OBO (delegated, capped by user),
- agent-as-principal (agent’s own rights),
- or raw impersonation (agent holds user’s full power).  
**Symptom:** Agent can do anything the user can, including things the user never intended in that task.

### 3. Confused deputy
A powerful gateway/MCP/runtime credential is reusable by any caller who can invoke it.  
**Symptom:** Prompt injection or another agent triggers high-privilege tools successfully.

### 4. Over-broad tool / API authorization grain
OAuth scopes / roles are API-coarse; agents need **tool- and argument-level** limits (`transfer 10` vs `transfer 1e6`).  
**Symptom:** “Authorized to payments API” ≈ authorized to any payment.

### 5. Standing / long-lived credentials
API keys, refresh tokens, DB passwords in agent config or memory.  
**Symptom:** Leak once → lasting blast radius; rotation rarely tied to agent lifecycle.

### 6. Workload identity ≠ business authorization
Cloud role proves *which compute*; it does not answer *on whose behalf* or *which business action*.  
**Symptom:** Teams think IRSA/IAM role “solved agent identity”; IGA/audit still empty.

### 7. Sub-agent / hop attenuation failure
Child agents inherit parent tokens or escalate.  
**Symptom:** Triage bot induces payments bot; no independent authZ on amount/account.

### 8. Shadow / ungoverned agents
Personal ChatGPT/IDE agents, unsanctioned Copilot bots, forgotten experiments with prod keys.  
**Symptom:** Discovery incomplete; kill switch cannot reach them.

### 9. Broken audit / non-repudiation
Missing dual attribution: **user + agent + tool + resource + decision**.  
**Symptom:** Incident response cannot answer “which agent, for whom, did what.”

### 10. Lifecycle / ownership gaps (IGA-shaped)
No joiner-mover-leaver for agents: owner left, agent still privileged; no access certification.  
**Symptom:** Orphan agents with prod entitlements.

### 11. HITL design failures
Either no human gate on material actions, or consent spam that people auto-approve.  
**Symptom:** Maker-checker theater; or agents blocked into uselessness so users bypass with personal tools (more shadow agents).

### 12. Session / memory isolation failures
Multi-user shared agent context.  
**Symptom:** Cross-user data or authority bleed.

---

## C. Problems by architecture pattern

| Pattern | Dominant identity issues |
|---|---|
| **Interactive chat + tools** | Impersonation defaults; prompt→tool without PEP; consent fatigue; per-session credential sprawl |
| **Long-running / autonomous** | Unattended subject unclear; standing refresh tokens; delayed HITL; change-mgmt on prompts/tools ignored |
| **Multi-agent orchestration** | Hop inflation; shared SA across nodes; incomplete `act` chains; SoD collapse |
| **SaaS-embedded (Copilot-class)** | Channel-dependent enforcement; connector OBO vs agent ID mismatch; registry ≠ uniform PEP |
| **Custom cloud agents** | Strong workload ID possible, weak agent directory/IGA; keys in task defs; DIY kill switch |
| **MCP tool plane** | Client with broad token to many servers; Enterprise-managed auth lagging adoption; passthrough brokers weaken binding |
| **RPA / UI automation** | Privileged bot accounts; password vaulting without agent identity; high confused-deputy |

---

## D. What “good” must eventually answer (problem → requirement)

For each material action, the architecture must make these answerable:

1. **Which workload** called?  
2. **Which agent** (unique principal)?  
3. **On whose authority** (user / sponsor / autonomous grant)?  
4. **What tool/args/resource** were allowed — and was that checked **outside** the model?  
5. **Can we revoke** this agent or session now?  
6. **Who owns** this agent in IGA terms?

If any answer is “unknown,” that is an identity issue — independent of which vendor you buy.

---

## E. Explicit non-goals for this note
- Not ranking Entra / Okta / CyberArk / Vault / AWS  
- Not proposing a lab or target architecture yet  
- Not a full threat model (STRIDE) — this is the **identity-issue inventory** derived from agent architectures  

Next research step (when you want): deepen any one problem class (e.g. confused deputy in MCP, or multi-agent attenuation) with concrete enterprise scenarios — still before vendor selection.
