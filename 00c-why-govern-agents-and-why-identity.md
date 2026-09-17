# Why govern agents? Why identity (especially for investment banks)?

**As of:** 2026-09-17  
**Focus:** Motivation and “why identity,” not a vendor bake-off.

---

## 1. Why govern agents at all?

Agents are not just chat UIs. In enterprise use they **initiate actions**: read mail and files, call APIs, write tickets, change code, move data, sometimes trigger money or access workflows.

Governance is needed because agents change three things at once:

1. **Speed** — mistakes and abuse happen at machine tempo (many tool calls per minute).  
2. **Opacity** — intent is in prompts and plans; classical logs that only show “app X called API Y” lose *who meant this* and *which agent*.  
3. **Scale of principals** — every new Copilot, studio agent, coding agent, and homemade LLM app is another actor. Without ownership and lifecycle, orphan and shadow agents accumulate.

If you only “govern models” (safety filters) or only “govern data” (DLP), you still may not know **which non-human actor** did what **on whose authority**, or how to **turn that one actor off**.

---

## 2. What can go wrong — especially in an investment bank?

Investment banks amplify the same failure modes with higher severity: regulated activity, market integrity, client confidentiality, and operational resilience.

| Harm class | Example agent path | Why it hurts a bank |
|---|---|---|
| **Confidentiality / client data** | Copilot or coding agent reads deal rooms, IB chat, CRM; personal ChatGPT gets pasted term sheets | Client confidentiality, wall-crossing, reputation, regulatory exposure |
| **Integrity of books & records** | Workflow agent posts journals, updates positions, “fixes” static data wrong | Financial misstatement, ops risk, hard-to-replay audit trail |
| **Unauthorized trading / payments adjacency** | Agent with broad API scope initiates or prepares transfers, broker instructions, or entitlement changes | Direct financial loss; maker-checker bypass if HITL is weak |
| **Privilege / access abuse** | Agent holds standing admin or shared SA; prompt injection → privileged tool | Insider-equivalent blast radius without an insider |
| **Market / conduct risk** | Agent drafts or sends client communications, research-like content, or manipulative patterns without supervision | Conduct rules, communications surveillance gaps |
| **Model / IP leakage** | Coding agent ships internal strategies, pricing logic, or secrets into PRs or external models | Competitive and security loss |
| **Resilience / concentration** | Critical process depends on unowned agent; owner left; no kill switch | BAU break; incident response cannot isolate the actor |
| **Regulatory accountability** | Examiner asks: which system acted, for whom, under what approval? | “The LLM did it” / shared bot SA is not an acceptable control story |

**Not every agent is high risk.** A read-only FAQ bot over public HR policy is different from an agent that can change entitlements or touch settlement systems. Governance should be **risk-tiered** — but identity is how you *know which tier* and *who owns it*.

---

## 3. Why “just use existing approaches” is not enough

| Approach | What it does well | What it cannot do alone for agents |
|---|---|---|
| **Shared service account + monitoring** | Simple to stand up | No per-agent attribution; kill one = kill many; SoD collapses |
| **Put everything under the human’s SSO session** (raw impersonation) | Feels natural for copilots | Agent can do *anything the user can*; confused deputy; no agent ceiling |
| **Network / egress controls only** | Limits destinations | Does not answer on-behalf-of; does not register agents; weak tool/arg grain |
| **DLP / content filters only** | Reduces some exfil | Does not govern *actions* (API writes); agents still act with standing creds |
| **Prompt / model safety only** | Reduces some bad completions | Not an authorization boundary; prompt injection bypasses “please don’t” |
| **Classic IGA only for humans** | Joiner-mover-leaver for people | Misses NHIs/agents unless agents are first-class identities with owners |
| **Cloud IAM roles only** | Strong workload attestation | Proves *which compute*, not *which agent persona* or *which user authority* |
| **Secrets manager without agent identity** | Rotates passwords | Still “something” fetched the secret; attribution and per-agent ceiling missing |

These remain **necessary layers**. Agentic identity does not replace DLP, PAM, or cloud IAM. It supplies the missing **principal and delegation model** those layers hang on.

---

## 4. What agentic identity uniquely contributes

**Agentic identity** means treating the agent as a governable actor in the identity fabric:

1. **First-class agent principal** — unique ID, owner/sponsor, lifecycle (create → certify → disable).  
2. **Clear acts-as model** — user OBO vs agent-as-principal vs forbidden impersonation; effective rights = intersection (user ∩ agent ∩ task).  
3. **Short-lived, audience-scoped credentials** — not standing keys in `.env`.  
4. **Policy enforcement outside the model** — tool/PEP checks that prompts cannot widen.  
5. **Attributable audit** — user + agent + tool + resource (+ hop chain for multi-agent).  
6. **Selective kill switch** — revoke one agent or session without collapsing a shared bot farm.

### Why other approaches can’t substitute for that

- **Without a distinct agent identity**, logs and IGA cannot answer “which agent?”  
- **Without delegation (OBO) caps**, “run as user” grants the agent the user’s full blast radius.  
- **Without agent ceiling policies**, even good OBO can be abused if the agent client is over-privileged.  
- **Without PEP outside the LLM**, safety text is not control.  
- **Without lifecycle/ownership**, banks accumulate orphan high-privilege automations — examiners and IR hate this.

So agentic identity is not “another chatbot feature.” It is the **control plane for non-human actors that reason and use tools**, which classical human-IAM and classical machine-IAM each only half-cover:

- Human IAM → people  
- Machine/workload IAM → compute and services  
- **Agentic identity → tool-using AI actors that act for people (or autonomously) with lasting enterprise side effects**

---

## 5. One-sentence pitch (bank-safe)

**We govern agents because they act; we use agentic identity because only a first-class, delegable, revocable agent principal makes those actions attributable and containable under investment-bank accountability — something shared accounts, prompts, DLP, or cloud roles alone cannot do.**

---

## Related notes
- Problems catalog: `00a-identity-problems-from-ai-architecture.md`  
- Agent types in enterprises: `00b-enterprise-agent-landscape.md`  
