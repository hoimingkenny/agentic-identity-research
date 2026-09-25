# Outline — Where does agentic IGA sit in AI frameworks?

**Status:** Structure + citations only (no full essay yet)  
**Audience:** IAM / Sec peers — concept alignment  
**Employer-agnostic**  
**Replaces:** Custom “L1–L5” as a claimed industry standard (optional internal nicknames OK if mapped)

---

## Primary question (Section 3)

> In published AI / agent frameworks, **where does agentic IGA (agent identity governance) sit?**  
> How does it differ from AI runtime/app security, traditional IGA, and enterprise AI risk management?

**One-line answer**

Agentic IGA governs **which agent, on whose behalf, may do what—provably and revocably**. In AI frameworks it sits in the **identity & privilege** control lane (notably OWASP ASI03 / NHI and CSA agent-identity guidance). It is **not** the whole of AI risk management, and **not** the same as prompt/runtime AI security.

---

## Position diagram (use in README / slides)

```
Enterprise AI risk management (NIST AI RMF / ISO 42001)
    └── Among many controls: Identity & access for AI systems
            ├── Humans + classic entitlements → Traditional IGA
            ├── Workload / machine identity → Cloud IAM / SPIFFE-class
            └── AI agents as first-class identities
                    + delegation (OBO) + tool/MCP authorization + revoke
                    → Agentic IGA / agent identity governance
                       Anchors: CSA AIGF; OWASP ASI03; OWASP NHI Top 10

Parallel (often confused, different job):
AI application / runtime security
    Anchors: OWASP Top 10 for LLM; parts of MAESTRO + GenAI Profile
    Typical products: prompt/guardrail / container AI security (e.g. AccuKnox-class)
```

**Read-out**

| If the question is… | Lane |
| --- | --- |
| Is the model/prompt/output/runtime behavior safe? | AI app / runtime security |
| Which **agent** may act, for whom, with which **tools**, and can we **revoke**? | **Agentic IGA** |
| How do we govern AI risk/use-cases org-wide? | NIST AI RMF / ISO 42001 (umbrella) |
| Which **person** may hold classic app/AD entitlements? | Traditional IGA |

---

## Citation anchors (primary sources)

### A. Identity-specific (strongest for “why agentic IGA”)

| Source | Use for |
| --- | --- |
| **OWASP Top 10 for Agentic Applications** — especially **ASI03 Identity and Privilege Abuse** | Names identity/privilege abuse as a top agentic risk → IGA lane |
| **OWASP Non-Human Identities Top 10** | Standing secrets, overprivilege, bad offboarding for non-humans |
| **CSA Agent Identity Governance Framework (AIGF) / NHI for agentic AI** | Lifecycle, owner, JIT, short-lived grants, revoke — “what agentic IGA does” |
| **CSA — Governing AI Agent Identities (maturity model)** | Stages toward inventory → owner → right-sized (JIT) → governed |

Links (verify when writing full doc):

- https://genai.owasp.org/ (Agentic Top 10 / GenAI project)  
- https://owasp.org/www-project-non-human-identities-top-10/  
- https://labs.cloudsecurityalliance.org/agentic/agentic-identity-governance-framework-v1/  
- https://cloudsecurityalliance.org/blog/2026/08/20/governing-ai-agent-identities-an-identity-maturity-model-for-ai-agents  

### B. Architecture threat layering (not a product RACI)

| Source | Use for |
| --- | --- |
| **CSA MAESTRO** (7 layers) | Where agents/tools/infra/ecosystem live; identity is **cross-cutting** (framework, infra, security, ecosystem)—not “one MAESTRO layer = IGA” |

- https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro  
- https://agentic-threat-modeling.github.io/MAESTRO/playbook/01-layers.html  

**Caution:** MAESTRO layer numbers ≠ any internal “L3 = IIQ / L4 = agentic” shorthand. If both appear in the pack, label them differently.

### C. Umbrella AI governance (process, not IAM product)

| Source | Use for |
| --- | --- |
| **NIST AI RMF 1.0** (Govern / Map / Measure / Manage) | Agentic IGA contributes to **Govern** and **Manage** (accountability, treatment)—not the whole RMF |
| **NIST AI 600-1** Generative AI Profile | GenAI-exacerbated risks (incl. security/privacy); maps actions to RMF |
| **ISO/IEC 42001** | AIMS: roles, inventory, oversight; complement technical IAM/IGA |

- https://www.nist.gov/itl/ai-risk-management-framework  
- https://nvlpubs.nist.gov/nistpubs/ai/nist.ai.600-1.pdf  
- https://www.iso.org/standard/42001  

### D. LLM application risks (adjacent, not agentic IGA)

| Source | Use for |
| --- | --- |
| **OWASP Top 10 for LLM Applications** | Prompt injection, disclosure, etc. → primarily **runtime/app AI security** |

---

## Draft section outline (when writing the full note)

1. **Question + one-line answer** (above)  
2. **Position diagram** + read-out table  
3. **OWASP:** LLM Top 10 vs Agentic Top 10 — draw the boundary; spotlight ASI03  
4. **CSA AIGF / maturity:** what “good” agent identity governance looks like (inventory, owner, JIT, revoke)  
5. **MAESTRO:** identity as cross-layer concern (short; avoid number collision)  
6. **NIST / ISO:** umbrella — agentic IGA is one control family under Govern/Manage / AIMS  
7. **Explicit non-goals:** does not replace traditional IGA; does not replace AccuKnox-class controls  
8. **Bridge to next pack sections:** hard In/Out scope of agentic IGA; then agents/architecture and how identity operates  

---

## Mapping from retired custom L1–L5 (optional footnote only)

| Old informal label | Prefer saying | Framework anchor |
| --- | --- | --- |
| L1 | AI app / runtime security | OWASP LLM; parts of MAESTRO / NIST GenAI Profile |
| L2 | Workload identity | Cloud IAM / SPIFFE; MAESTRO infra; NHI |
| L3 | Traditional IGA | Classic IGA / CCM–AICM IAM baseline |
| L4 | Agentic IGA | CSA AIGF; OWASP ASI03; NHI for agents |
| L5 | AI use-case / model risk governance | NIST AI RMF; ISO 42001 |

Do **not** present the left column as an external standard.

---

## Done criteria for this outline

- [x] No dependency on L1–L5 as industry numbering  
- [x] Clear “where agentic IGA sits” answer  
- [x] Named professional anchors with roles  
- [ ] Full prose + deep quotes (later content pass)  
- [ ] GitHub publish (later, after Kenny OK)
