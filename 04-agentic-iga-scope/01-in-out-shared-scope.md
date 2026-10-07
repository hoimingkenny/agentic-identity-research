# Outline — Agentic IGA / agent identity: In / Out / Shared scope

**Status:** Structure for logic-chain link **#4** (boundary must not be fuzzy)  
**Parent:** [`00-logic-chain-locked.md`](./00-logic-chain-locked.md)  
**Related:** [`08-outline-where-agentic-iga-sits.md`](./08-outline-where-agentic-iga-sits.md)  
**Employer-agnostic**

---

## Primary question

> What does **Agent Identity / Agentic IGA** govern—and what does it **explicitly not** govern?

**One-line answer**

It governs the **agent as a first-class identity**: ownership, lifecycle, delegation (OBO vs impersonation), **authorization to use tools/MCP**, dual attribution, and **identity-plane revoke**. It does **not** own prompt/jailbreak defenses, container hardening, human JML as SoR, or enterprise AI use-case approval—those stay in other lanes (with clear handoffs).

---

## Design rules for this section

1. Prefer a **table** over prose lists so peers can argue cell-by-cell.  
2. Every “Out” cell names **who/what owns it instead**.  
3. “Shared” means **RACI handoff**, not “both do the same thing.”  
4. If a vendor pitch collapses In+Out into “AI security,” treat as **out of scope for this definition**.

---

## IN — Agentic IGA / agent identity **does** this

| # | In scope | Means in practice | Evidence / PoC hint |
| --- | --- | --- | --- |
| I1 | **Agent as first-class identity** | Not only a renamed service account; distinct principal, metadata, status | Create/suspend/delete agent object |
| I2 | **Human owner (sponsor)** | Every agent linked to a person/team; owner change / leaver impacts agent | Owner left → agent flagged or disabled |
| I3 | **Lifecycle** | Register, certify (optional), transfer, decommission | Inventory + offboarding path |
| I4 | **Delegation model** | Prefer **OBO / delegation** over silent impersonation; make “acts as” explicit | Token or audit shows agent + user |
| I5 | **JIT / short-lived access** | Standing admin tokens discouraged; time-bound, scoped grants | TTL; no eternal shared secret as sole model |
| I6 | **Tool / MCP authorization policy** | Allow/deny which tools/actions (finer than app login / AD group alone) | Allow tool X, deny Y |
| I7 | **Dual (or multi) attribution** | Audit: **user → agent → resource/action** | One call export for investigator |
| I8 | **Identity-plane kill / revoke** | Disable agent identity and invalidate/constrain credentials in policy | Kill → subsequent calls fail |
| I9 | **Discovery / inventory of agents** *(as far as feasible)* | Known + push to reduce shadow; honest about limits | Inventory API / connectors / IDE signals |
| I10 | **Policy & review language for agents** | Who may own agents; which tools are high-risk; review cadence | Written policy + sample review |

**Framework anchors for “In”:** CSA AIGF / agent identity maturity; OWASP ASI03; OWASP NHI Top 10 (where agents are NHIs).

---

## OUT — Agentic IGA **does not** own (name the owner)

| # | Out of scope | Belongs instead to | Why separate |
| --- | --- | --- | --- |
| O1 | Prompt injection / jailbreak / toxic output filtering | AI app / runtime security (OWASP LLM; guardrail products) | Content/behavior of model ≠ entitlement of agent |
| O2 | PII/secret exfil detection in model I/O | Runtime / DLP / AI security controls | Data path inspection ≠ identity SoR |
| O3 | Container / K8s / sandbox hardening | Platform + runtime security | Workload isolation ≠ who the agent is |
| O4 | Foundation model safety / eval / red-team of the model itself | Model risk / AI eng / MAESTRO L1-style concerns | Different lifecycle and owners |
| O5 | **Human** JML, AD groups, classic app entitlements as SoR | **Traditional IGA** | Agents consume owners/people from here; do not replace it |
| O6 | Break-glass **human** privileged session recording (classic PAM) | PAM | May coordinate later; not the same control |
| O7 | Business approval of “may we use AI for use-case X?” | AI use-case / model risk governance (NIST RMF / ISO 42001 style) | Policy of *whether* vs *which identity may* |
| O8 | Full product RBAC meaning inside each business app | App owners / developers | IGA assigns; app enforces business rules |
| O9 | Generic enterprise secrets vaulting alone | Secrets management | Vaulting ≠ agent authorization or dual audit |
| O10 | Network firewall / EDR malware response | Classic cyber | Posture ≠ agent standing access governance |

---

## SHARED — handoffs (not duplicate ownership)

| Concern | Agentic IGA | Other lane | Handoff |
| --- | --- | --- | --- |
| S1 Kill switch | Disable identity / revoke grants | Runtime stops process; gateway denies | IGA signals + PEP/runtime enforce |
| S2 Audit | Identity attribution (user↔agent↔action) | SIEM stores/alerts; runtime logs prompts | Common IDs / correlation keys |
| S3 High-risk tool | Policy: tool is restricted | Runtime may still block bad content | Deny-by-identity **and** deny-by-behavior |
| S4 Owner truth | Agent owner binding | Traditional IGA / HR / directory = person SoR | Sync people → agent owners |
| S5 Workload trust | Agent may require attested runtime | Workload identity (SPIFFE / cloud IAM) | Prove *where* before *what* |
| S6 Shadow agents | Inventory & policy pressure | Endpoint / IDE / CASB-class signals | Discovery feeds IGA; IGA cannot see all alone |

---

## Fuzzy-boundary anti-patterns (call out in the full doc)

1. “Our AI gateway is IGA” — gateway may be **PEP**; policy/SoR still needed.  
2. “AccuKnox-class replaces SailPoint/agent IGA” — different questions (behavior vs standing/delegated access).  
3. “IIQ AD group = agent tool authorization” — too coarse; wrong object.  
4. “Secrets manager = agent governance” — storage ≠ who may call what.  
5. “NIST AI RMF means we bought agentic IGA” — RMF is umbrella process, not an identity product.

---

## Bridge

- **From §3:** frameworks place agentic IGA in identity/privilege → §4 lists concrete In/Out cells.  
- **To §5:** with boundary fixed, show agents/architectures and the **operating loop** (inventory → issue → enforce → audit → kill).

---

## Done criteria for full write-up later

- [ ] In/Out/Shared tables filled with org-neutral language  
- [ ] Every Out has an owner lane  
- [ ] No cell says “AI security” without splitting runtime vs identity  
- [ ] Cross-link to locked chain §1–3 and §5  

---

*Outline only. Full prose and GitHub publish on request.*
