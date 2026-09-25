# What agents exist in the enterprise?

**Focus:** Landscape of agent *types* enterprises actually run or will run — not vendor IAM products.  
**As of:** 2026-09-17  
**Audience:** Finance/enterprise-applicable research  

This sits beside the identity-problem catalog (`00a`). First know **which agents**, then which identity issues apply.

---

## 1. Why this matters

“Agent” in enterprise speech mixes very different things:

- A **chat assistant** that answers from SharePoint  
- A **coding agent** that edits repos and opens PRs  
- A **workflow agent** that calls finance/HR APIs  
- A **custom LLM app** someone built to automate a team process  
- A **personal** ChatGPT/IDE agent with pasted prod credentials  

Identity controls must differ by type. Treating them as one “AI bot” SA is how attribution and blast radius fail.

---

## 2. Enterprise agent taxonomy (practical)

### A. Productivity / knowledge copilots (SaaS-embedded)

| Examples | What they do | Typical runtime |
|---|---|---|
| **Microsoft 365 Copilot** | Chat over mail, files, meetings; org knowledge | Microsoft-hosted, user session |
| **Declarative / Copilot Chat extensions** | Ground answers in tenant knowledge; light actions | Copilot chat harness |
| **SharePoint / Agent Builder–style agents** | Site/team Q&A, lightweight knowledge agents | M365 / SharePoint |

**Architecture fit:** Interactive chat + tools; SaaS-embedded.  
**Dominant identity issues:** Channel-dependent enforcement; connector OBO vs agent ID; shadow “helpful” agents with broad Graph scopes; consent fatigue.

---

### B. Low-code / studio business agents

| Examples | What they do | Typical runtime |
|---|---|---|
| **Copilot Studio (standard harness)** | Topic/flow-style assistants, predictable dialogs | Power Platform |
| **Copilot Studio (GitHub Copilot harness)** | Multistep reasoning, connectors, MCP, files, longer workflows | Copilot Studio sandbox + connectors |
| **Power Automate + AI actions** | Triggered workflows with LLM steps | Cloud flows / desktop flows |

**Architecture fit:** Interactive and/or long-running; tool plane = connectors + MCP + APIs.  
**Dominant identity issues:** Maker vs runtime identity; connector credentials; agent publish lifecycle; who owns production agent after maker leaves.

---

### C. Coding / software-engineering agents

| Examples | What they do | Typical runtime |
|---|---|---|
| **GitHub Copilot** (chat, agent, PR agents) | Code completion, repo agents, CI/PR automation | IDE + GitHub-hosted agent runtimes |
| **Cursor / Claude Code / Codex-class tools** | Local or cloud coding agents with tools, terminals, MCP | Developer laptop or cloud VM |
| **OpenCode** (and similar open coding agents) | Open/self-hosted or local coding agent stacks | Dev machine / self-hosted |
| **CI coding bots** | Auto-fix, dependency bumps, review bots | GitHub Actions / runners |

**Architecture fit:** Tool-rich agents; often **developer-as-user** + **workload** on runner/VM; MCP and shell are high-blast tools.  
**Dominant identity issues:** Agent acting with developer’s GitHub/cloud tokens; repo secrets in agent context; PR bots as NHIs; laptop agents outside IGA; prompt→`git push` / cloud deploy without PEP.

**Finance note:** Coding agents that can reach staging/prod credentials or customer data repos are material-risk agents, not “just IDE features.”

---

### D. Custom LLM apps that automate workflows (“someone built an agent”)

| Examples | What they do | Typical runtime |
|---|---|---|
| Internal GPT wrappers + tools | Summarize tickets, draft emails, update CRM | App service / container |
| LangChain / LlamaIndex / Semantic Kernel apps | Multi-step business automation | Custom cloud |
| “Chat with our DB/docs” apps | RAG + optional write actions | Custom cloud |
| Department scripts calling OpenAI/Azure OpenAI APIs | Semi-agentic batch jobs | Cron / Functions |

**Architecture fit:** Custom cloud; chat+tools or long-running.  
**Dominant identity issues:** Shared service account / API keys in `.env`; no agent-as-principal; confused deputy if one backend identity serves all users; no sponsor/lifecycle in IGA.

This is often the **largest unmanaged surface** in banks and brokerages: useful, fast to build, invisible to IAM.

---

### E. Cloud platform “agent products”

| Examples | What they do | Typical runtime |
|---|---|---|
| **Amazon Bedrock Agents / AgentCore** | Orchestrated agents with tools, knowledge bases | AWS |
| **Azure AI Foundry / Agent Framework agents** | Pro-code agents, publish into M365/Teams possible | Azure |
| **Google Vertex / Gemini agent platform + Agent Identity** | Cloud agents with SPIFFE-style agent principals | GCP |

**Architecture fit:** Custom cloud / platform-managed; strong **workload identity** options.  
**Dominant identity issues:** Workload ID ≠ business OBO; DIY IGA; secrets for outbound SaaS; multi-agent hop control.

---

### F. RPA + “AI agents”

| Examples | What they do | Typical runtime |
|---|---|---|
| UiPath / Automation Anywhere + LLM skills | UI automation with AI decisions | Robots / orchestrators |
| Legacy screen scrapers wrapped with LLM | Same, with less governance | Desktops / VDI |

**Architecture fit:** RPA tool plane.  
**Dominant identity issues:** Privileged bot accounts; password vaulting without clean agent identity; very high confused-deputy risk.

---

### G. Domain / vendor-embedded agents

| Examples | What they do |
|---|---|
| Salesforce / ServiceNow / SAP / security vendors’ “agents” | Act inside that SaaS with vendor identity models |
| Security “AI SOC” / identity brokers’ agents | Ticket triage, enrichment, sometimes remediations |

**Architecture fit:** SaaS-embedded or custom.  
**Dominant identity issues:** Second IdP/agent registry; sync to enterprise IGA often weak; privileged remediations need PAM/HITL.

---

### H. Shadow / personal agents (still “in” the enterprise)

| Examples | What they do |
|---|---|
| Personal ChatGPT / Claude with pasted tickets or keys | Ad-hoc work outside sanctioned tools |
| Unapproved IDE agents with prod `.env` | Code + infra access |
| Unregistered Copilot Studio experiments | Semi-prod connectors |

**Architecture fit:** Anything — unmanaged.  
**Dominant identity issues:** Shadow agents; credential sharing; zero inventory; no kill switch.

---

## 3. Map type → architecture pattern → identity pressure

| Agent type | Primary patterns | Identity pressure (from `00a`) |
|---|---|---|
| M365 Copilot / declarative | Chat + SaaS-embedded | OBO, channel PEP, Graph scope sprawl |
| Copilot Studio workflow agents | Chat + long-running + connectors/MCP | Maker/runtime split, connector secrets, lifecycle |
| Coding agents (Copilot, OpenCode, Cursor…) | Tool-rich interactive + sometimes CI long-running | Dev token inheritance, shell/MCP blast radius, laptop outside IGA |
| Custom LLM workflow apps | Custom cloud chat/batch | Shared SA, no agent principal, confused deputy |
| Bedrock / Foundry / Vertex agents | Custom cloud / platform | Workload≠OBO, DIY IGA |
| RPA+AI | RPA | Privileged bot accounts |
| Shadow personal agents | Unmanaged | Discovery + credential paste |

---

## 4. How enterprises usually “get” agents (procurement / build paths)

1. **Buy seat products** — M365 Copilot, GitHub Copilot Business/Enterprise  
2. **Build in studio** — Copilot Studio / Power Platform  
3. **Build custom** — internal LLM apps, AgentCore/Foundry, open stacks (LangChain, OpenCode-like)  
4. **Enable by accident** — IDE agents, browser extensions, personal SaaS  
5. **Vendor ships agents inside existing apps** — CRM, ITSM, security tools  

Identity programs that only cover (1) miss (3)–(5), where many failures start.

---

## 5. Suggested inventory questions (problem-first)

For each agent found in the enterprise, answer:

1. **Category** (A–H above)?  
2. **Who is the user** when it runs (employee, system, both)?  
3. **What can it change** (read-only vs write vs money/move access)?  
4. **Where do credentials live** (user OBO, connector, `.env`, cloud role, vault)?  
5. **Is it registered** in any directory/IGA with an owner?  
6. **Can we kill it** without breaking an unrelated bot sharing the same SA?

If (5) or (6) is “no,” that is already an identity issue — before buying new IAM features.

---

## 6. Explicit non-goals
- Not ranking which coding agent is “best”  
- Not a full Microsoft licensing guide  
- Not prescribing Entra/Okta/CyberArk yet — only classifying agents so identity problems can be applied accurately  

**Next:** optionally deep-dive one vertical (e.g. coding agents in a bank, or Copilot Studio agents with finance connectors) still on the problem side.
