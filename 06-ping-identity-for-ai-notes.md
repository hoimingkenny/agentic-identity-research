# Notes — Ping Identity “Identity for AI” module

**Source hub:** https://developer.pingidentity.com/identity-for-ai/index.html  
**As of:** 2026-09-18  
**Purpose:** Capture how Ping frames agentic identity (IdP/AS layer) and map it to this research pack.

---

## 1. What the module is

Ping’s **Identity for AI** developer module is a documentation set for securing AI agents with identity protocols — not a CNAPP, not an IGA suite. Center of gravity: **IdP / authorization server** patterns (PingFederate, PingOne, Advanced Identity Cloud, PingAM).

Hub sections (from the index): What’s new, Get started, Agent fundamentals, Agent identity and access, Protocols, Use cases, Integrations, Glossary.

---

## 2. Core thesis (Ping’s framing)

AI no longer only supports human actions — it **performs** them. As systems move from predictive models to autonomous agents, a new identity challenge emerges: **Identity for AI**.

That aligns with this pack’s problem statement (`00a`, `00c`): agents are actors; classical human-session impersonation and shared secrets are unsafe.

---

## 3. Agent taxonomy (Ping)

| Type | Trust boundary | Character |
|---|---|---|
| **Unmanaged / personal agents** | Outside org boundary | User’s personal agent hitting your APIs; not governed by you |
| **Managed — digital assistants** | Inside org | Org-created, user-facing; act for users; preregistered with IdP/AS |
| **Managed — digital workers** | Inside org | More autonomous; may run with own sign-on; HITL for high-sensitivity; monitor like a workforce identity |

Useful for whitepaper estate language (`00b`).

---

## 4. Design patterns Ping emphasizes

### 4.1 Delegation over impersonation (RFC 8693 token exchange)

**Problem:** If an app/agent holds a token that lets it do anything the user can, behavior is indistinguishable from the user — dangerous in multi-agent flows.

**Pattern:** OAuth 2.0 Token Exchange (RFC 8693):
- Agent never holds long-lived “live” user credentials.
- After user-triggered exchange, agent gets a **finely scoped** access token.
- JWT keeps **`sub`** = user (authority) and nested **`act`** = actor chain (which agents).

Example chain (from Ping docs):
1. User → Agent1 (`sub`=user, `aud`=agent1, no `act` yet)  
2. Agent1 → Agent2 (`sub`=user, `aud`=agent2, `act.sub`=agent1)  
3. Agent2 → MCP (`sub`=user, `aud`=mcp, nested `act` for agent2←agent1)

**Value:** Accountability (audit) + authorization (policy on user **and** which actors).

Primary page: https://developer.pingidentity.com/identity-for-ai/identity/idai-token-exchange.html

### 4.2 Workload identity (SPIFFE) ≠ authorization

- **SPIFFE/SVID** answers: *which workload is calling?*  
- JWT-SVID can be used as **`actor_token`** in token exchange (e.g. with PingFederate validating via SPIRE OIDC JWKS).  
- A valid SPIFFE identity does **not** by itself grant business permissions — policy still decides what that workload may do.

Blog / playground alignment: https://developer.pingidentity.com/blog/securing-agentic-workflows-with-token-exchange-and-workload-identity/

### 4.3 MCP as OAuth-protected capability plane

- MCP servers expose tools/resources/prompts; must not rely on static secrets.  
- Prefer OAuth 2.0, audience-restricted tokens, fine-grained scopes, **per-tool** authorization.  
- AuthZ at MCP is separate from “model said so.”

### 4.4 Related protocol vocabulary (glossary)

Agentic identity, NHI, MCP, A2A, CIBA (async HITL), DCR (dynamic client registration), RAR, PAR, trust boundary — see Ping glossary under the same module.

---

## 5. Binding-point scorecard (this pack’s lens)

| Binding point | Ping Identity for AI fit | Notes |
|---|---|---|
| Workload identity | **Strong** | SPIFFE/SVID as attested actor |
| User OBO / delegation | **Strong** | RFC 8693, `sub`/`act`, `may_act` |
| Agent-as-principal (IdP client) | **Strong** | Preregister / DCR as OAuth clients |
| Standing SA / secret sprawl | **Discouraged** | Exchange + short-lived tokens preferred |
| PEP outside the model | **AS + MCP/resource** | Scopes/claims; resource must enforce |
| IGA / SailPoint JML & certs | **Out of primary scope** | IdP layer, not access certification SoR |
| Runtime sandbox (eBPF/K8s) | **Out of primary scope** | Contrast AccuKnox-class CNAPP |

---

## 6. Compose with SailPoint and runtime (architect view)

```
Ping / enterprise IdP  → user auth, agent client registration, token exchange,
                          SPIFFE actor validation, MCP audience tokens
SailPoint              → owner/sponsor, JML cascade, entitlements certs, SoD
Runtime (e.g. AccuKnox)→ discover AI assets; sandbox process/tool; optional SPIFFE
PAM / Vault            → privileged secrets / JIT for high-risk tools
```

**One-liner:** Ping’s Identity for AI is a strong **IdP/AS reference architecture** for agentic identity protocols. It validates delegation ≠ impersonation and workload ID ≠ authZ. It does **not** replace SailPoint lifecycle governance or kernel-level runtime containment.

---

## 7. What you should be able to explain (after this module)

1. Why forwarding the user token to another agent/MCP is wrong.  
2. How nested `act` claims support multi-agent audit.  
3. How SPIFFE JWT-SVID works as `actor_token` with PingFederate-style exchange.  
4. Personal vs digital assistant vs digital worker.  
5. Where Ping stops and SailPoint / runtime begin.

---

## 8. Key links

- Hub: https://developer.pingidentity.com/identity-for-ai/index.html  
- Token exchange / agents: https://developer.pingidentity.com/identity-for-ai/identity/idai-token-exchange.html  
- Agent types: https://developer.pingidentity.com/identity-for-ai/identity/idai-agent-types.html  
- MCP + OAuth: https://developer.pingidentity.com/identity-for-ai/identity/idai-securing-mcp-servers-oauth.html  
- SPIFFE playground: https://developer.pingidentity.com/identity-for-ai/protocols/authplayground-spiffe.html  
- Token exchange playground: https://developer.pingidentity.com/identity-for-ai/protocols/authplayground-token-exchange.html  
- Blog (exchange + workload identity): https://developer.pingidentity.com/blog/securing-agentic-workflows-with-token-exchange-and-workload-identity/

---

## Related pack notes
`00a` problems · `00b` landscape · `00c`/`00d` why IAM & traditional security aren’t enough · `05` architect Q&A · AccuKnox chat note (runtime complement)
