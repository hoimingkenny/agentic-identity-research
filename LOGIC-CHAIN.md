# Locked logic chain (1–5) — IGA → agentic identity

**Status:** Locked for pack structure / peer alignment (2026-09-24)  
**Purpose:** One spine. Every major doc answers one link. Thick research hangs under the spine; do not reorder casually.  
**Audience:** Self + IAM/Sec peers  
**Employer-agnostic**

---

## Product one-liner

> A **thick** research pack with a **single question-driven path**: traditional IGA in security → honest limits of traditional IGA → where **agentic IGA** sits in published AI frameworks → hard **In/Out** scope → what agents/architectures are and **how** agent identity operates.

---

## The chain (read in order)

| # | Primary question | One-line answer | Must include | Must not steal from later |
| --- | --- | --- | --- | --- |
| **1** | In **traditional cybersecurity**, what is the role of **central IGA**? | Governs **standing access** (who should hold which keys)—not FW/EDR/MFA; shrinks attack surface and blast radius with or without external attack. | Stack position vs FW/EDR/IdP/PAM/SIEM; with vs without attack; why orgs use IGA (P1–P6 style). | Agent/MCP detail; vendor shortlist. |
| **2** | What are traditional IGA’s **limits**—including former “strengths”? Why still required? | Still required for **humans + classic entitlements**; historically weak on **discovery/coverage** (known connectors/accounts only); those gaps **explode** for shadow agents. | Honest critique (discovery, coarse entitlements, human-speed cert); “needed ≠ enough for agents.” | Full agent binding map; claiming IGA is obsolete. |
| **3** | In **published AI frameworks**, where does **agentic IGA** sit? | In the **identity & privilege** lane (OWASP ASI03 / NHI; CSA AIGF)—not LLM runtime security, not the whole NIST/ISO umbrella. | Position diagram; NIST/OWASP/CSA/ISO anchors; vs AccuKnox-class vs traditional IGA. | Custom “industry L1–L5”; product bake-off. |
| **4** | What does Agent Identity / IGA **govern**—and what does it **not**? | Hard **In / Out / Shared** boundary so the edge is not fuzzy. | In: agent as principal, owner, lifecycle, OBO vs impersonation, tool/MCP authz policy, dual audit, identity-plane revoke. Out: prompt/jailbreak/PII, container hardening, human JML SoR, full AI use-case committee. | Soft “AI security does everything.” |
| **5** | Under real **AI agents**, what are agents & architectures, and **how** does agent identity/IGA operate? | Short “what is an agent / common patterns,” then how identity **does work**: inventory, credentials, PEP/gateway, audit, kill. | Architecture → binding points → control loop; enough to explain Monday-morning tool calls. | Replacing §1–4; endless vendor features. |

---

## Story arc (one breath)

1. Central IGA exists because **standing access** is a security control plane.  
2. It remains necessary for people/classic access, but **discovery and agent-shaped access** expose structural limits.  
3. In AI frameworks, **agentic IGA** is the identity/privilege lane (ASI03 / AIGF), beside—not inside—runtime AI security and umbrella AI RMF.  
4. Draw a **non-fuzzy** In/Out border for that lane.  
5. Connect to **real agents/architectures** and show the **operating loop** of agent identity.

---

## Success check (alignment)

A peer can answer without slides:

1. Why central IGA in traditional security?  
2. Why still keep it, and where it falls short (incl. discovery)?  
3. Where agentic IGA sits among NIST/OWASP/CSA (not “all AI security”)?  
4. What is in vs out of agentic IGA?  
5. What is an agent (briefly), and how does identity/IGA act on tool use / revoke?

---

## Pack mapping (structure only; content later)

| Link | Planned home (names flexible) | Existing material to hang under |
| --- | --- | --- |
| 1 | Foundations — why central IGA | `01-foundations-traditional-iga/*` (Phase A) |
| 2 | Why traditional IGA is not enough (incl. discovery) | Expand from `00d` + new discovery critique |
| 3 | Where agentic IGA sits in AI frameworks | `08-outline-where-agentic-iga-sits.md` |
| 4 | In/Out scope | `09-outline-agentic-iga-in-out-scope.md` |
| 5 | Agents, architecture, how identity operates | `00a`/`00b`/`01` + `from-lab-bot/01` + JIT/OBO/kill notes |

Deduplicate root vs `from-lab-bot` **under** this spine (Phase B+).

---

## Explicitly secondary (this pass)

RFP scoring, management budget slides, public portfolio polish — later **exports** from the same spine, not a different spine.

---

*Locked. Change only by revising this file and re-aligning section READMEs.*
