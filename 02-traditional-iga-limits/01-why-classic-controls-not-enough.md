# Why traditional security measures are not enough for agents

**Scope:** Red teaming, AI guardrails, and application security — necessary, not sufficient.  
**As of:** 2026-09-18  
**Audience:** IAM / security architects (company whitepaper ammunition)

---

## Thesis in one line

Red teaming finds weaknesses, guardrails reduce bad model behavior, and AppSec hardens software — **none of them create a first-class, delegable, revocable agent principal or enforce “user ∩ agent ∩ task” on every tool call.** Agentic identity (with IGA) supplies that control plane.

---

## 1. What each traditional measure is for

| Measure | Primary job | Typical output |
|---|---|---|
| **Red teaming / adversarial testing** | Discover how systems can be abused (prompt injection, jailbreaks, tool misuse, exfil paths) | Findings, severity, remediation backlog |
| **Guardrails** (input/output filters, topic blocks, safety classifiers, allow/deny topic lists) | Reduce harmful or out-of-policy **model generations** | Blocked/rewritten responses; sometimes blocked tool suggestions |
| **Application security** (SDLC, SAST/DAST, dependency scanning, secure design, API authz in the app) | Make the **software** that hosts agents less vulnerable | Patches, secure configs, app-layer authZ bugs fixed |

All three belong in an enterprise AI security program. They answer different questions than IAM.

---

## 2. Why they fall short for **agent** risk

### Red teaming
**Does well:** Proves agents can be tricked into over-privileged tools; surfaces confused-deputy and data-exfil scenarios; pressure-tests HITL.

**Cannot replace identity because:**
- It is **point-in-time assurance**, not continuous authorization.  
- A clean red-team report does not register agents, assign owners, or run JML when a maker leaves.  
- Findings often say “agent had standing admin token” — the fix is **identity/least privilege/lifecycle**, not another annual exercise.  
- You cannot red-team every shadow IDE agent and homemade LLM app at bank scale.

**Architect line:** *Red team tells you the blast radius; identity limits and attributes the blast radius every day.*

### Guardrails
**Does well:** Blocks toxic content, some PII patterns in chat, jailbreak-ish prompts; useful UX safety layer.

**Cannot replace identity because:**
- Guardrails sit on **language**, not on **enterprise entitlements**. An agent can pass safety filters and still call `transfer_funds` if the tool credential allows it.  
- Prompt injection and tool-calling paths often **bypass** “please don’t” instructions; authorization must live in a **PEP outside the model**.  
- Guardrails rarely encode SoD, maker-checker, or “this agent may not exceed the user’s access package.”  
- They do not produce examiner-grade audit of *which agent principal* acted *on whose behalf*.

**Architect line:** *Guardrails constrain speech; identity constrains action.*

### Application security
**Does well:** Secure coding for agent hosts, API authN/Z bugs, dependency risk, threat modeling of the app.

**Cannot replace identity because:**
- AppSec secures **an application**; enterprises run **many** agents across Copilot, Studio, coding tools, and custom apps — identity must be **cross-platform**.  
- Fixing an IDOR in one API does not create **agent-as-principal**, sponsor, or leaver-disable in SailPoint.  
- Classic AppSec assumes relatively stable service identities; agents multiply principals and change tools dynamically (MCP).  
- “The app validated a JWT” ≠ “effective rights were user ∩ agent ∩ task with attributable agent ID.”

**Architect line:** *AppSec hardens the house; agentic identity issues badges, keys with expiry, and who is allowed in which rooms.*

---

## 3. Side-by-side: security measure vs identity control objective

| Control objective (bank IAM language) | Red team | Guardrails | AppSec | Agentic identity + IGA |
|---| :---: | :---: | :---: | :---: |
| Discover abuse paths | ●●● | ● | ●● | ● (via design) |
| Block toxic/unsafe text | ● | ●●● | ● | ○ |
| Secure the hosting code/API | ● | ○ | ●●● | ● (consumes secure APIs) |
| Unique agent principal + owner | ○ | ○ | ○ | ●●● |
| Delegation: user ∩ agent ∩ task | ○ | ○ | ● partial (per app) | ●●● |
| Continuous authZ outside the model | ○ | ○ | ● per app | ●●● cross-estate |
| Selective kill / leaver cascade | ○ | ○ | ○ | ●●● |
| Access certs / SoD on agent entitlements | ○ | ○ | ○ | ●●● (SailPoint-shaped) |
| Estate-wide inventory of agents | ○ | ○ | ○ | ●●● |

○ = not the job · ● = partial · ●●● = primary fit

---

## 4. How they should work **together** (compose, don’t compete)

```
Red team     → finds that Agent X can reach Tool Y with standing key
Guardrails   → reduce bad prompts / unsafe completions in the UX path
AppSec       → ensures Tool Y’s API and the agent host aren’t trivially owned
Agentic IAM  → Agent X is a registered principal; only narrow JIT/OBO token;
               PEP denies Y unless policy allows; owner + cert + kill switch
SailPoint    → lifecycle/ownership/certs for Agent X and its entitlements
```

**Whitepaper phrasing:** Traditional AI and AppSec controls reduce vulnerability and unsafe content. They do not substitute for extending IAM to agents. Without agentic identity, the bank still cannot systematically answer who acted, on whose behalf, with what standing privilege, or how to revoke one agent without collapsing a shared service account.

---

## 5. FAQ traps to avoid
- “We red-teamed Copilot, we’re fine.” → Assurance ≠ continuous control.  
- “We have Azure/OpenAI content filters.” → Content ≠ entitlement.  
- “The agent app passed AppSec.” → One app ≠ estate identity program.  
- “Identity is only SSO.” → SSO for humans does not bound agent tool rights.

---

## Related
`00c` why govern / why identity · `04` whitepaper (fold this into §5) · `05` architect Q&A
