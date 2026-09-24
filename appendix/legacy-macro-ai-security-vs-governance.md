# Macro view: AI security vs AI / agent identity governance

**Audience:** Kenny (IIQ engineer; IT Security has AccuKnox)  
**Date:** 2026-09-22  
**Purpose:** High-level framing for why IIQ stays, why a new agent-management solution is needed, and how that differs from AccuKnox-class AI security.

---

## One-sentence thesis

**AccuKnox-class tools ask “is this AI behavior / workload safe?”**  
**IIQ asks “should this human (or classic account) have this entitlement?”**  
**Agentic identity / IGA asks “which agent, for whom, may call which tool now — and can we prove and revoke it?”**  
Those are different control planes. Keeping IIQ does not close the agent gap; buying only prompt/container guardrails also does not.

---

## Layer map (macro)

| Layer | Question it answers | Example controls | Your stack today |
| --- | --- | --- | --- |
| **L1 – AI / runtime security** | Is the model interaction or host safe? | Prompt firewall, jailbreak/injection block, PII redaction, container/K8s runtime (eBPF), sandbox, AI-SPM | **AccuKnox** (guardrails, container security, prompt injection) |
| **L2 – Workload / platform identity** | Which compute/workload is calling? | IRSA, task roles, SPIFFE, mTLS | Cloud IAM / platform (partial) |
| **L3 – Human + classic NHI IGA** | Who owns access; JML; cert; SoD on accounts/entitlements | Connectors, cubes, roles, certifications, provisioning | **SailPoint IIQ (keep)** |
| **L4 – Agentic identity / agentic IGA** | Agent as first-class principal: owner, lifecycle, acts-as (OBO vs agent-as-principal), tool/MCP inventory, dual audit, kill switch | Agent registry, owner mapping, delegated tokens, MCP gateway PEP (IdP/AM), agent certs | **Gap — researching SailPoint Agentic Fabric + Okta/Ping/etc.** |
| **L5 – Business / model risk governance** | Should we deploy this use case? Model risk, policy, HITL design | MRM, AI use-case register, ethics/risk committees | Org process (out of IAM tooling) |

**Rule for exec discussions:** Do not let vendors collapse L1–L4 into one “AI security” slide. AccuKnox strengthens L1; IIQ is L3; the new buy is primarily **L4** (with some discovery overlap to negotiate carefully).

---

## Why traditional IIQ cannot solve agentic AI (high-level)

IIQ was built for: **Identity → Accounts → Entitlements → Request / Cert / Provision** over applications.

An agent action is not that shape. One tool call mixes:

1. runtime/workload  
2. which agent  
3. on whose behalf (user / agent-own / shared SA)  
4. which tool/resource (often argument-sensitive)  
5. sometimes sub-agents  

### What IIQ still does well (keep it)
- Human JML, role/SoD, entitlement certification  
- Classic service-account ownership / orphan cleanup (awkward but known)  
- Authoritative **human owner** directory for any agent platform  

### What IIQ structurally misses for agents
| Agent problem | Why IIQ falls short |
| --- | --- |
| No first-class agent principal | Cube/account model ≠ agent instance + tools + MCP |
| Shadow agents | SAP/Zoom/DB connectors do not see IDE/Copilot/MCP sprawl |
| Delegation vs impersonation | No native OBO / `act` / task-scoped agent token story |
| Tool/arg authorization | Entitlements are app-coarse, not `transfer(amount)` |
| Machine-speed change | Quarterly cert ≠ continuous agent privilege |
| Kill switch | Disable correlated account ≠ stop runtime + revoke short-lived tokens |
| Dual attribution audit | Strong on “who has access”; weak on “user+agent+tool+resource this call” |

**Punchline for leadership:** IIQ remains the system of record for **people and classical access**. Agents create a **new identity type and a new runtime authorization path** that IIQ was never designed to be the control plane for.

---

## Why a new solution (while keeping IIQ)

You need a control plane that can at least:

1. **Inventory** sanctioned + (as far as possible) shadow agents  
2. **Bind** each agent to human owner(s) / sponsor (IIQ humans can feed this)  
3. **Lifecycle** agents (create → change tools/creds → retire) independent of human JML  
4. **Clarify acts-as** (OBO vs agent-as-principal; ban silent impersonation)  
5. **Constrain tools** (scopes / gateway PEP; ideally with IdP or AM partner)  
6. **Audit & revoke** with dual attribution and a real kill path  

SailPoint **Agentic Fabric / Agent Identity Security** is one candidate that sits next to IIQ (IIQ can sync humans for ownership). Okta for AI Agents / Ping Identity for AI are peers that often sit next to an IdP/AM stack. **Complement, don’t rip IIQ.**

---

## AccuKnox vs agentic IGA (avoid double-counting)

**AccuKnox (L1) typically covers:**
- Prompt injection / jailbreak firewall and content guardrails  
- PII/secret leakage in prompts/responses  
- Container / K8s / runtime sandboxing for AI workloads  
- AI posture / inventory of models and some agent compute surfaces  

**AccuKnox does not replace L4:**
- Who is the business owner of agent X when the owner leaves?  
- Should agent X be certified to call Finance MCP this quarter?  
- Is this call user-delegated or agent-own authority?  
- Enterprise-wide agent SoD / access certification campaigns  
- Identity-native kill switch across IdP sessions + agent registry (IGA-shaped)

**Overlap to manage in RFP:** both may claim “shadow AI discovery” and “least privilege for agents.” Split the ask:
- AccuKnox = **prevent unsafe behavior on the wire/host**  
- Agentic IGA = **govern who the agent is and what access it is allowed to hold**  
- IdP/AM gateway (Okta/Ping/etc.) = **mint/exchange tokens and enforce at tool call**

Prompt injection blocked by AccuKnox still leaves a confused-deputy risk if the agent holds a standing admin token — that is an **identity** failure, not a prompt failure.

---

## Slide-ready narrative (5 lines)

1. We keep **SailPoint IIQ** for human and classical access governance.  
2. Agentic AI breaks IIQ’s model because one action is not one human entitlement.  
3. **IT Security already invests in AccuKnox** for prompt/container/runtime AI security (L1).  
4. The remaining gap is **agentic identity governance (L4)**: owner, lifecycle, delegation, tool access, dual audit, kill switch.  
5. We are evaluating **SailPoint Agentic Fabric** and peer solutions (Okta/Ping/…) as a **complement** to IIQ + AccuKnox — not a replacement for either.

---

## Open decisions (for later research, not this note)

- Fabric vs Okta vs Ping: discovery breadth vs runtime PEP GA maturity  
- How IIQ remains SoR for humans while agent directory lives in Fabric/ISC  
- Whether MCP gateway sits in IdP/AM or in SailPoint’s fabric story  
- Shared RACI: SecOps (AccuKnox) vs IAM (IIQ + agentic IGA) vs platform (cloud IAM)
