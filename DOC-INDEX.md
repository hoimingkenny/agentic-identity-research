# Document index — question each doc answers + one-line summary

**How to use:** Read [LOGIC-CHAIN.md](./LOGIC-CHAIN.md) first, then sections **01 → 05**. This page maps *what each file is for*.  
**Employer-agnostic.**

---

## Spine

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [LOGIC-CHAIN.md](./LOGIC-CHAIN.md) | What is the locked 1–5 path for this research pack? | Single question-driven spine from traditional IGA → agentic IGA scope → how agent identity operates. |
| [README.md](./README.md) | Where do I start in this repo? | Pack entry point: reading order, section table, links to migration. |
| [MIGRATION.md](./MIGRATION.md) | Where did old paths go after the reorg? | Old→new path map; notes removal of flat root and parallel `from-lab-bot/` duplicates. |
| [DOC-INDEX.md](./DOC-INDEX.md) | What question does each doc answer? | This index: per-doc question + short summary. |

---

## 01 — Why central IGA? (logic-chain link #1)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./01-why-central-iga/README.md) | What belongs in section 01? | Section index for link #1. |
| [01-security-position.md](./01-why-central-iga/01-security-position.md) | In traditional cybersecurity, what is central IGA's job vs FW/EDR/IdP/PAM/SIEM? | IGA governs **standing access** (who should hold which keys), shrinking surface and blast radius—not perimeter or runtime detection. |
| [02-vs-app-teams.md](./01-why-central-iga/02-vs-app-teams.md) | Why not leave access only to each app/dev team? | Access is an enterprise accountability problem; app teams own in-app roles well but fail at cross-system consistency, leavers, approval evidence, and SoD. |

---

## 02 — Traditional IGA limits (logic-chain link #2)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./02-traditional-iga-limits/README.md) | What belongs in section 02? | Section index for link #2; flags planned discovery-critique note. |
| [01-why-classic-controls-not-enough.md](./02-traditional-iga-limits/01-why-classic-controls-not-enough.md) | Why are classic AI/AppSec controls (and traditional IGA alone) not enough for agents? | Red team, guardrails, and AppSec matter but do not create a first-class, delegable, revocable agent principal or enforce user∩agent∩task on tool calls. |

---

## 03 — Where agentic IGA sits (logic-chain link #3)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./03-agentic-iga-in-ai-frameworks/README.md) | What belongs in section 03? | Section index for link #3; do not treat custom L1–L5 as an industry standard. |
| [01-where-agentic-iga-sits.md](./03-agentic-iga-in-ai-frameworks/01-where-agentic-iga-sits.md) | In published AI frameworks, where does agentic IGA sit? | Outline: identity & privilege lane (OWASP ASI03/NHI, CSA AIGF)—beside runtime AI security, under NIST/ISO umbrellas; not the whole AI RMF. |
| [02-management-1pager.md](./03-agentic-iga-in-ai-frameworks/02-management-1pager.md) | How do we explain the gap to management without collapsing layers? | Scope-locked 1-pager: keep classic IGA and AccuKnox-class runtime; fund agent identity/IGA as a complementary lane. |

---

## 04 — Agentic IGA scope (logic-chain link #4)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./04-agentic-iga-scope/README.md) | What belongs in section 04? | Section index for link #4. |
| [01-in-out-shared-scope.md](./04-agentic-iga-scope/01-in-out-shared-scope.md) | What does agent identity/IGA govern—and what does it not? | Hard **In / Out / Shared** tables so the edge is not fuzzy (principal, owner, OBO, tool/MCP authz, dual audit, identity revoke vs prompt/container/JML SoR). |

---

## 05 — Agents, architecture, operations (logic-chain link #5)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./05-agents-architecture-operations/README.md) | What belongs in section 05? | Section index for link #5. |
| [01-identity-problems-from-architecture.md](./05-agents-architecture-operations/01-identity-problems-from-architecture.md) | What identity problems do real agent architectures create? | Problem catalog: one tool call mixes workload, agent, human authority, and resource—classical IAM assumptions break. |
| [02-enterprise-agent-landscape.md](./05-agents-architecture-operations/02-enterprise-agent-landscape.md) | What kinds of agents actually show up in the enterprise? | Practical taxonomy (copilots, coding agents, workflow agents, shadow/personal, etc.) so controls can differ by type. |
| [03-why-govern-agents.md](./05-agents-architecture-operations/03-why-govern-agents.md) | Why govern agents, and why through identity? | Motivation: speed, opacity, and scale of principals; regulated harm classes show why attribution and revoke matter. |
| [04-ai-architecture-patterns.md](./05-agents-architecture-operations/04-ai-architecture-patterns.md) | How do common agent runtimes and tool planes map to identity controls? | Runtime shapes (chat+tools, autonomous, multi-agent, SaaS-embedded, custom cloud) and MCP/function-calling → control binding. |
| [05-identity-binding-map.md](./05-agents-architecture-operations/05-identity-binding-map.md) | Where exactly must identity bind in agent architectures? | Deeper binding-point map (workload, user OBO, agent-as-principal, kill shared SA) with research citations. |

---

## 06 — Vendors (supporting depth)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./06-vendors/README.md) | What belongs in section 06? | Section index; one canonical Tier-1 matrix. |
| [01-tier1-matrix.md](./06-vendors/01-tier1-matrix.md) | How do Tier-1 vendors score on agent identity binding points? | Canonical comparison (AuthN, OBO, MCP, authZ grain, audit, kill, IGA hooks) with maturity labels. |
| [02-okta-ping-ibm-iga-vs-agentic.md](./06-vendors/02-okta-ping-ibm-iga-vs-agentic.md) | How do Okta / Ping / IBM split traditional IGA vs agentic identity? | Side-by-side: IGA strength vs agentic GA/EA posture relative to keeping SailPoint-class IGA. |
| [03-ping-identity-for-ai-notes.md](./06-vendors/03-ping-identity-for-ai-notes.md) | How does Ping frame "Identity for AI"? | Working notes on Ping's IdP/AS-centric agent module and taxonomy mapped to this pack. |

---

## 07 — Landing / PoC (supporting depth)

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./07-landing/README.md) | What belongs in section 07? | Section index; one canonical playbook. |
| [01-playbook-skeleton.md](./07-landing/01-playbook-skeleton.md) | How would an enterprise land agentic identity in phases? | Phased outline (preconditions → discover → design → pilot → scale) without product scaffold. |
| [02-shortlist-poc-matrix.md](./07-landing/02-shortlist-poc-matrix.md) | What must a ~6-month shortlist/PoC prove? | Capability matrix skeleton with locked must-haves (owner, JIT/OBO, tool authz, dual audit, kill) and non-goals. |
| [03-architect-qa-checklist.md](./07-landing/03-architect-qa-checklist.md) | What should an architect be able to answer—and ask? | Dual-lens checklist: credibility answers + discovery questions for IGA + agentic identity. |
| [04-whitepaper-outline.md](./07-landing/04-whitepaper-outline.md) | How structure an internal whitepaper on why classic IAM is not enough? | Outline for a control-gap whitepaper (NHI + agents) that extends existing IAM rather than replacing it. |

---

## Appendix

| Doc | Question it answers | One-line summary |
| --- | --- | --- |
| [README.md](./appendix/README.md) | What is in the appendix? | Index of digests, legacy notes, temporary deprecated duplicates. |
| [executive-summary.md](./appendix/executive-summary.md) | What was the pack's early executive framing? | Older v1 exec summary (binding points + research purpose)—refresh later against the locked chain. |
| [discussion-digest.md](./appendix/discussion-digest.md) | What research decisions were locked in early discussion? | Digest of purpose, priority (problems before vendors), and paused scaffolding. |
| [legacy-macro-ai-security-vs-governance.md](./appendix/legacy-macro-ai-security-vs-governance.md) | How did we previously layer AI security vs IGA vs agent identity? | Pre-framework macro L1–L5 note—**superseded** by §03 framework positioning; keep for history only. |
| `deprecated-*` | What did the pre-dedupe duplicate copies look like? | Temporary second copies of vendor matrix / playbook for one-release diff; safe to delete after review. |

---

## Reading order (short)

1. Spine → **LOGIC-CHAIN**  
2. **01 → 05** for the argument  
3. **06 / 07** when comparing vendors or designing a PoC  
4. **Appendix** only for legacy / migration context  
