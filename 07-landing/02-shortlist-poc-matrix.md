# Shortlist / PoC capability matrix (skeleton)

**Purpose:** Compare L4 agentic identity candidates for the enterprise within ~6 months  
**Non-goals:** Replace AccuKnox; replace IIQ; assume Okta-only IdP  
**Must fit:** IIQ (human SoR) + Keycloak API/SSO plane + AD/Entra + CyberArk (PAM team) + mixed runtimes (PC IDE + K8s/Tanzu + cloud)

---

## Candidate columns (fill as research deepens)

| ID | Candidate | Type | Notes |
| --- | --- | --- | --- |
| A | SailPoint Agentic Fabric (+ IIQ humans as owners) | IGA / agent identity | Primary SailPoint path while keeping IIQ |
| B | Microsoft Entra agent / workload identity features | IdP / cloud identity | Already in directory landscape |
| C | Okta agent identity / Auth0 / Gateway (if any) | IdP / AM | Only if adding Okta is acceptable |
| D | Ping Agent Gateway / PingOne Advanced | AM / PEP | Strong runtime PEP story historically |
| E | Keycloak-centric (token exchange + custom PEP) | Build / extend | Fits current API auth plane; more engineering |
| F | CyberArk / PAM agent or secrets path | PAM | Same team; clarify vs L4 IGA |
| G | _(other)_ | | e.g. Astrix, Oasis, Aembit, SPIRL—as research warrants |

Add/remove columns freely; do not score vendors until product claims are verified against docs/PoC.

---

## Scoring legend

- **M** = Must for PoC pass  
- **S** = Should  
- **N** = Nice  
- Score: 0 = absent / incompatible · 1 = roadmap/preview · 2 = partial · 3 = demonstrated in PoC  
- **Fit** = works with IIQ keep + Keycloak + no AccuKnox replacement

---

## A. PoC must-haves (Kenny-locked)

| # | Capability | Pri | A Fabric | B Entra | C Okta | D Ping | E Keycloak+ | F CyberArk | Evidence / how to test |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A1 | First-class **agent** identity (not only service account rename) | M | | | | | | | Create agent object; lifecycle independent of human |
| A2 | **Human owner** bind; sync owners from IIQ/AD | M | | | | | | | Owner offboard → agent flagged/disabled |
| A3 | **JIT** short-lived credentials | M | | | | | | | TTL measured; no standing admin token |
| A4 | **OBO** / delegation (not impersonation-only) | M | | | | | | | Token shows agent + user; app can distinguish |
| A5 | **Tool / MCP-level** authz (beyond app login / AD group) | M | | | | | | | Allow tool X deny tool Y; arg constraints if claimed |
| A6 | Dual audit **user → agent → resource** per action | M | | | | | | | Export one call trail for auditor |
| A7 | **Kill switch** + revoke evidence | M | | | | | | | Disable agent; in-flight token fails < N sec |
| A8 | Clear **RACI vs AccuKnox** (L1 vs L4) | M | | | | | | | One-pager: who owns prompt vs who owns agent access |

---

## B. Environment fit (enterprise-specific)

| # | Fit criterion | Pri | A | B | C | D | E | F | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| B1 | IIQ remains human/classic SoR (overlay, not rip-replace) | M | | | | | | | |
| B2 | Works with **Keycloak** OIDC/API plane (or clear federation) | M | | | | | | | Today: SSO + AD group claims |
| B3 | AD and/or Entra as identity source | S | | | | | | | |
| B4 | Coexistence with **CyberArk** (no forced PAM rip) | S | | | | | | | Agent path may differ from human PAM |
| B5 | Staff **PC / IDE / MCP** agents (shadow) discoverable or controllable | M | | | | | | | OpenCode-class |
| B6 | Server agents on **K8s / Tanzu** | S | | | | | | | |
| B7 | Public cloud agents (AWS / Azure) | S | | | | | | | AWS ISC already in IIQ |
| B8 | Provides or integrates **PEP / Agent Gateway** (today: direct MCP/API) | M | | | | | | | No central gateway today |
| B9 | Does **not** claim to replace AccuKnox L1 | M | | | | | | | Disqualify overlap-only pitches |

---

## C. Governance / ops (should)

| # | Capability | Pri | A | B | C | D | E | F | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| C1 | Agent inventory / discovery (incl. shadow where possible) | S | | | | | | | |
| C2 | Lifecycle: create / suspend / delete / transfer owner | S | | | | | | | |
| C3 | Policy as code or certifiable agent access reviews | S | | | | | | | Explicit SoD later |
| C4 | SIEM / audit export | S | | | | | | | |
| C5 | Break-glass with dual control | N | | | | | | | |
| C6 | Sub-agent / multi-agent attribution | N | | | | | | | |

---

## D. Disqualifiers (automatic fail for this program)

1. “AI security” pitch that only covers prompt/injection/container (→ AccuKnox lane)  
2. Requires **IIQ → ISC migration** as prerequisite for agent features  
3. Only AD-group / app-login controls with no tool-level path  
4. Standing long-lived shared secrets as the only agent credential model  
5. No kill / revoke story for short-lived tokens or runtime  
6. Cannot show dual attribution (user + agent) in audit  

---

## E. Suggested PoC scenarios (minimal set)

| Scenario | Proves |
| --- | --- |
| **P1** Coding agent on staff PC calls one internal API via MCP | Shadow/IDE path + PEP or equivalent |
| **P2** Server agent on K8s/Tanzu calls finance-like API with OBO | JIT + OBO + dual audit |
| **P3** Owner leaves / agent disabled | Kill switch + owner lifecycle via IIQ person record |
| **P4** AccuKnox still blocks a bad prompt while L4 denies an unauthorized tool | L1 vs L4 complementarity demo |

---

## F. RACI sketch (fill with names later)

| Concern | IAM/IGA (IIQ) | AI Security (AccuKnox) | Cloud/Platform | App owners |
| --- | --- | --- | --- | --- |
| Agent identity & owner | **R/A** | C | C | C |
| Tool/MCP allow policies | **A** / R with apps | C | C | **R** |
| Prompt / jailbreak / PII egress | C | **R/A** | C | I |
| Container / K8s runtime hardening | C | **R/A** | **R** | I |
| Human JML / AD groups / IIQ | **R/A** | I | I | C |
| CyberArk human privileged | **R/A** | I | C | C |
| Agent token issue/revoke (IdP/AM/gateway) | A | I | R/C | C |

---

## G. Next research actions

1. Deep-dive **SailPoint Agentic Fabric** against rows A1–A8 + B1–B9  
2. Map **Entra** agent features vs keeping Keycloak as API plane  
3. Decide whether **Ping/Okta** are politically viable given Keycloak SSO  
4. Clarify CyberArk human JIT with PAM SMEs; document agent ≠ human PAM unless proven  
5. Score only after vendor docs / demo; keep this matrix as living artifact  

---

*Skeleton only—scores intentionally blank.*
