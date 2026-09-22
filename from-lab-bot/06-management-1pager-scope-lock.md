# Management 1-pager: Why L4 Agentic Identity (enterprise finance context)

**Audience:** IAM/Sec peers + upward budget narrative  
**Date:** 2026-09-22  
**Status:** Scope-locked from stakeholder Q&A (Kenny / IAM Lab Bot)  
**Non-goal:** Does **not** replace AccuKnox (prompt / container / guardrails)

---

## One-line ask

Keep **SailPoint IdentityIQ** for humans and classic access. Keep **AccuKnox** for AI/runtime security. Fund a **new L4 agentic identity / IGA** capability (SailPoint Agentic Fabric and/or peers) because agents are a new identity type that today's IIQ + AD-group + Keycloak SSO path cannot govern.

---

## Macro stack (do not collapse into "AI Security")

| Layer | Question answered | Today (enterprise example) |
| --- | --- | --- |
| **L1** AI / runtime security | Is this AI/workload behavior safe? | **AccuKnox** (other team) |
| **L2** Workload identity | Which compute/process is calling? | Cloud IAM / K8s SA / etc. (partial) |
| **L3** Human + classic NHI IGA | Should this person/account have these entitlements? | **SailPoint IIQ (keep)** |
| **L4** Agentic identity / IGA | Which agent, on whose behalf, which tool *now*—provable & revocable? | **Gap** |
| **L5** Business / model risk | Is this use case allowed? | Process / governance |

**Punchline:** AccuKnox ≠ agentic IGA. IIQ ≠ agent control plane. New spend **complements** both.

---

## Why IIQ alone cannot solve agentic AI

IIQ is built for: **Identity → Account → Entitlement → Request / Cert / Provision**.

A single agent tool call mixes: runtime + agent principal + human represented + tool/MCP (often with arguments) + sometimes sub-agents.

| IIQ still owns | Structural gap for agents |
| --- | --- |
| Human JML, SoD, entitlement cert | No first-class **agent** object |
| Service-account ownership patterns | Shadow IDE/MCP agents invisible to classic connectors |
| Human owner directory for any agent platform | No OBO / task-scoped token model |
| Classic app AD-group provisioning | Entitlements too coarse for tool/MCP |
| | Quarterly cert ≠ machine-speed change |
| | Disable AD account ≠ stop runtime + revoke short-lived tokens |
| | Audit strong on "who has rights"; weak on "this call: user→agent→tool→resource" |

**Current access path (confirmed):** IIQ requests app **AD group(s)** → user SSO via **Keycloak (OIDC)** → Keycloak returns AD groups → app authorizes by group. That is human/app login—not agent JIT/OBO/tool authorization.

---

## What "new solution" must prove (PoC bar)

Within ~6 months we may need shortlist / PoC. Pass criteria:

1. Agent as **first-class identity** + **human owner** (IIQ can sync people as owners)
2. **JIT** short-lived tokens, preferably with **OBO**
3. **Tool / MCP-level** authorization (not only app login)
4. **Dual audit:** user → agent → resource (per action)
5. **Kill switch** / immediate revoke with evidence
6. Clear **RACI vs AccuKnox** (no duplicate "AI security" buy)

---

## Environment constraints (shortlist must fit)

- **IIQ stays** long-term; agent capability **overlays** (Fabric and/or others)—not rip-and-replace / not forced ISC migration
- **SSO:** Keycloak ↔ LDAP / Windows AD  
- **Directory:** Windows AD + Microsoft Entra ID  
- **API auth today:** Keycloak (SSO/OIDC; group claims)—token exchange / fine agent scopes **not** the current main path  
- **PAM:** CyberArk managed by same IAM team; human JIT details TBD—do **not** assume agents reuse CyberArk JIT  
- **Runtimes:** staff PC coding agents (e.g. OpenCode/MCP), in-house on **K8s / Tanzu**, public cloud (AWS/Azure), many unknowns  
- **Tool path today:** **no central agent/MCP gateway**—direct API/MCP calls → PoC likely needs a **PEP / Agent Gateway** story  
- **Compliance:** no explicit written agent policy yet; design to best practice with dual attribution + kill-path evidence  
- **Ownership:** L4 under **IAM/IGA (IIQ team)** inside IT Security; AccuKnox owned by **separate** AI Security team  

---

## Five-slide narrative

1. Keep IIQ for people and classic access.  
2. Agentic AI means one action ≠ one human entitlement—IIQ's model is insufficient alone.  
3. AccuKnox already covers L1 (prompt / container / runtime).  
4. Remaining gap is **L4 agentic identity governance**.  
5. Evaluate Agentic Fabric + peers as **complement to IIQ + AccuKnox**, replacing neither.

---

## Decision requested from leadership

Approve research → vendor shortlist → PoC against the six criteria above, owned by IAM/IGA, coordinated with AI Security (AccuKnox) so budgets and RACI stay complementary.
