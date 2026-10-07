# Internal whitepaper outline — Why traditional IAM is not enough for NHI & agentic identity

**Author posture:** IAM engineer — today: human identity, service accounts, JML lifecycle; roadmap: NHI + agentic identity  
**Audience:** Security / IAM leadership, risk, application owners, platform/cloud  
**Tone:** Control-gap analysis, not a product pitch  
**Status:** Outline + core argument draft for Kenny’s company whitepaper  

---

## Suggested title options
1. *Extending IAM Beyond Humans and Service Accounts: Preparing for NHI and Agentic Identity*  
2. *Why Classical IAM Controls Are Necessary but Not Sufficient for AI Agents*  
3. *From JML to Agents: Closing the Accountability Gap in Tool-Using AI*

**Recommended:** (1) — positions you as extending the existing program, not replacing it.

---

## Whitepaper structure (8–12 pages)

### 1. Executive summary (½–1 page)
- What we already do well: workforce IAM, service accounts, JML.  
- What changed: AI agents and automations that **reason + use tools** at scale.  
- Thesis: traditional controls remain mandatory foundations; they do **not** by themselves provide per-agent identity, constrained delegation, attributable audit, or selective revocation for agents.  
- Ask: endorse a phased program — inventory → pilot identity binding → IGA/PAM integration for agents/NHI.

### 2. Scope and definitions (1 page)
| Term | Meaning in this paper |
|---|---|
| Human identity | Workforce users, SSO, MFA, JML |
| Service account / NHI (classical) | Non-human creds for apps, batch, integrations |
| Agent | Tool-using AI runtime that plans/acts (Copilot-class, coding agents, studio agents, custom LLM apps) |
| Agentic identity | First-class principal + delegation model + lifecycle for agents |
| JML | Joiner–Mover–Leaver for humans (and, by extension, what agents still lack) |

Explicit non-goals: choosing a single vendor; building a lab product in this paper.

### 3. Current-state IAM (your program) (1 page)
Describe **as-is** without apology:
- Human identity: authN, authZ, SSO/MFA, access reviews as applicable  
- Service accounts: provisioning patterns, vaulting/rotation where present, ownership gaps if honest  
- JML: joiner/mover/leaver for people; what happens to automations when owners leave  

**Point:** The whitepaper extends this program — it does not claim human/SA IAM failed.

### 4. What agents exist in our kind of enterprise (1–1.5 pages)
Reuse taxonomy from `00b` (condensed for company):
- M365 Copilot / knowledge agents  
- Copilot Studio / low-code workflow agents  
- Coding agents (GitHub Copilot, IDE agents, OpenCode-class)  
- Custom LLM workflow apps  
- Cloud platform agents; RPA+AI; vendor-embedded; shadow/personal  

Inventory call-to-action: we likely have more agents than IAM can currently name.

### 5. Core section — Why traditional approaches are not enough (3–4 pages) ⭐
This is the heart. Use a **control-objective** frame your bank already understands.

#### 5.1 Control objectives that still apply
Accountability, least privilege, SoD / maker-checker, auditability, timely revocation, ownership.

#### 5.2 Mapping: traditional control → gap under agents

| Traditional approach | What it was designed for | Gap when agents arrive |
|---|---|---|
| **Workforce IAM + JML** | People join/move/leave | Agents are not employees; no standard JML for agent principals; orphan agents after owner leaves |
| **SSO / MFA for humans** | Prove a person | Does not register *which agent* acted; MFA does not bound tool calls |
| **“Run as the user” (session reuse / impersonation)** | Convenience for apps | Agent inherits full user blast radius; confused deputy; no agent ceiling |
| **Shared service accounts** | Simple app-to-app auth | Many agents → one SA; cannot attribute or revoke one agent; SoD collapses |
| **Classical NHI / SA inventory (if any)** | Static bots, batch IDs | Misses dynamic IDE agents, Copilot agents, short-lived MCP clients, shadow agents |
| **Secrets vault / rotation alone** | Protect passwords/keys | Still “something” fetched the secret; no per-agent policy or delegation story |
| **Cloud IAM roles / IRSA alone** | Workload / compute identity | Proves *which runtime*, not *on whose behalf* or *which agent persona* |
| **DLP / content security alone** | Stop sensitive text egress | Does not authorize *actions* (API writes, entitlement changes, merges) |
| **Prompt / model safety alone** | Reduce bad completions | Not an authorization PEP; prompt injection bypasses instructions |
| **Network egress allowlists alone** | Limit destinations | No tool/arg grain; no user∩agent intersection; weak attribution |
| **Access reviews (human-centric)** | Certify people’s access | Agents’ entitlements and tool grants often invisible to reviewers |
| **PAM for human privileged sessions** | Supervise admin humans | Agents may hold standing privileged API paths without session broker or JIT |

#### 5.3 The accountability gap (one diagram in prose)
```
Human JML ──► Human principal ──► SSO/MFA ──► Apps
                      │
                      ✗ breaks when actor is an agent using tools

Service account ──► Shared machine principal ──► Apps
                      │
                      ✗ cannot express “Agent A on behalf of User U for Tool T”
```

**Required answers for material actions (examiners / IR / SoD):**
1. Which **workload**?  
2. Which **agent**?  
3. On whose **authority**?  
4. Which **tool/args/resource**, checked **outside** the model?  
5. Can we **revoke** this agent/session now?  
6. Who **owns** it in IGA terms?

Traditional stacks answer (1) partially and (human) poorly for (2)–(6).

#### 5.4 Investment-bank severity (short)
Confidentiality (walls/clients), books & records, privileged abuse, conduct/comms, IP leakage, resilience when unowned agents underpin BAU — see `00c`.

### 6. What “good” looks like — agentic identity as program extension (1.5–2 pages)
Position as **extension of IAM**, not a side AI project:

1. First-class agent (and broader NHI) principals  
2. Owner/sponsor + JML-like lifecycle for agents  
3. Delegation model: user OBO ∩ agent ceiling ∩ task scope  
4. Short-lived credentials; reduce standing SA usage for agents  
5. PEP outside the model (gateway/broker/IdP policy)  
6. Audit: user + agent + tool + resource (+ hops)  
7. Selective kill switch  
8. Compose with existing PAM/secrets/cloud IAM — do not rip and replace  

### 7. Phased recommendations (1 page)
| Phase | Outcome |
|---|---|
| 0–30d | Inventory agent types; classify risk; ban shared-SA pattern for new high-risk agents |
| 30–90d | Pilot: one attended OBO path + one bounded autonomous path; define audit fields |
| 90–180d | IGA hooks (owner, certify, leaver disables agents); PAM/JIT for privileged tools |
| Ongoing | Standards for custom LLM apps; coding-agent policy; shadow-agent detection |

### 8. Asks / decisions needed
- Executive sponsor (CISO / Head of IAM)  
- System of record for agent identities (align with IdP/IGA direction)  
- Risk tiers and HITL rules for material actions  
- Mandate: no production agent without owner + revoke path  

### 9. Appendix
- Glossary  
- Agent taxonomy (`00b` summary)  
- Problem catalog (`00a` summary)  
- References (internal policies + external standards as you add them)

---

## Draft thesis paragraph (paste-ready)

> Our IAM program correctly governs human identities through authentication, authorization, and JML, and manages service accounts as classical non-human credentials. Those controls remain necessary. They are not sufficient for AI agents: tool-using systems that plan and act across enterprise applications. Agents introduce new principals that are neither employees nor static batch accounts. Reusing human sessions (impersonation), shared service accounts, cloud workload roles alone, secrets vaults alone, DLP, or prompt safety cannot simultaneously provide per-agent attribution, constrained delegation (on whose behalf), policy enforcement outside the model, and selective revocation. Agentic identity extends IAM—alongside broader NHI governance—so that every material agent action is attributable, least-privileged, owned, and killable, consistent with the accountability expectations of an investment bank.

---

## How this connects to your career narrative
You are not “leaving IAM for AI.” You are the natural owner of the next IAM surface: **from human + SA + JML → NHI + agentic identity**, with a whitepaper that educates the firm on the control gap.

---

## Related research in this repo
- `00a` problems · `00b` agent landscape · `00c` why govern / why identity · `01`–`03` architecture & vendors (later depth)
