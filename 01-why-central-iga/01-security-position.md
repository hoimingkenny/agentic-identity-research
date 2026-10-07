# Why central IGA? (cybersecurity view)

**Audience:** Security / IAM practitioners and readers new to IGA  
**Status:** Foundations (Phase A) — employer-agnostic  
**Related:** [02 — IGA vs app-team access](./02-iga-vs-app-team-access.md) · later: why agentic identity (existing `00c` / `00d`)

---

## One-line answer

Central IGA is not a firewall and not EDR. Its **security job** is to govern **which identities are allowed to hold which standing access**—shrinking attack surface and blast radius whether or not an external network attack is underway.

People adopt IGA because many serious incidents are not “the wall was breached” alone; they are **“an identity used rights it should never have had, or that should already have been revoked.”**

---

## Where IGA sits in the security stack

| Layer | Typical tools | Question answered |
| --- | --- | --- |
| Perimeter / traffic | Firewall, WAF, VPN | Which connections are allowed? |
| Host / malware | EDR, AV | Is this host/process trustworthy? |
| Authentication | IdP, MFA, SSO | Are you who you claim to be? |
| **Access governance (IGA)** | **SailPoint-class platforms** | **Should this identity hold these rights; who approved; when revoked?** |
| Privileged execution | PAM | How are high-risk elevations time-bounded and recorded? |
| Detect / respond | SIEM, SOC | Is something bad happening *now*? |

**IGA = the governance plane for “what standing access should exist.”**  
Most other controls answer “what is allowed **in this moment**.”

**Principle P1 — Authentication ≠ authorization governance.**  
MFA/IdP prove *who you are*. IGA governs *how many keys you carry day to day*.

**Principle P2 — IGA owns standing access; many security tools own “this request.”**

---

## With a network attack vs without one

### A. When there *is* an external or lateral attack

Attackers often want a **usable identity**, then exercise **rights already granted** (credential theft, session hijack, malware running as a user/service).

IGA’s security contribution:

- **Shrink attack surface** — fewer standing privileges, fewer stale entitlements, fewer orphan/shared accounts  
- **Shrink blast radius** — even if an account is taken, damage is smaller if admin/cross-system combos are rare  
- **Speed cut-off** — Joiner/Mover/Leaver and disable/deprovision supply a coherent “what to revoke” list for IdP/PAM/SOC  
- **Investigation** — who had what, who approved — supports response and accountability  

IGA does **not** replace exploit blocking, malware defense, or C2 detection. Those are EDR/FW/WAF territory.  
IGA covers what happens **after** an identity is usable: how dangerous the **permission map** is.

### B. When there is *no* external network attack

High-impact events often need no internet breach:

- Insider misuse (rights used “legitimately” but wrongly)  
- Leavers / role changes with rights left behind  
- Over-broad roles added “for convenience”  
- Shared and ownerless service accounts  
- Contractors whose access outlives the engagement  

These are **identity hygiene** problems. IGA remains a primary control: prevent **lawful identities with unlawful standing access** from accumulating.

**Principle P3 — Needed with and without external attack.**  
With attack: reduce blast radius and enable revoke. Without attack: stop permission hygiene collapse.

---

## Why it matters (security language)

**IGA decides how much harm remains after you decide to trust an identity.**

If everyone is over-privileged after MFA succeeds, the rest of the security stack fights on a flat plain of excess rights.

Security-ordered value:

1. Reduce unnecessary **effective** standing privilege (prevention)  
2. Keep rights aligned with **HR / org change** (continuous hygiene)  
3. See **dangerous cross-system combinations** (SoD — security and fraud control overlap)  
4. Give responders an executable **revoke list** (detection needs a governance source for response)

---

## What IGA uniquely does (given so many other tools)

| Other tools do well | IGA is for |
| --- | --- |
| EDR: kill malicious processes | Not “what roles this person should hold across 50 apps” |
| Firewall: IP/port policy | Not “whether this AD group should exist” |
| SIEM: alert on odd logins | After alert, “which entitlements to disable” needs a source of truth |
| IdP/MFA: strong proof of identity | Proof ≠ least privilege; groups can still be huge |
| PAM: elevate for high-risk ops | Does not own lifecycle of every business entitlement |
| Each app’s own RBAC | Local only — no enterprise JML or unified revoke |

**What central IGA actually does for security:**

- Drive create/change/remove of accounts and groups across systems from join/move/leave  
- Require request + approval before rights appear (less “engineer clicked admin”)  
- Force periodic access review (“do you still need this?”)  
- Find orphan, shared, ownerless accounts  
- Export “who has high-risk rights” as an attack-surface list for SOC/red team  
- Feed downstream enforcement (directory groups, SSO claims, app roles)

**What IGA is not (avoid wrong expectations):**

- Not a prompt firewall, CVE scanner, or EDR replacement  
- Not the PEP for every API/tool call (that is gateway/AM; agents need an extra layer later)  
- Not a promise that “with IGA you cannot be breached” — it makes misuse of identity **less catastrophic and faster to unwind**

**Principle P4 — IGA does not replace detect/protect; it governs over-privilege and lifecycle.**

---

## Why organizations actually use IGA

Compliance often funds the program. **Security reasons people keep using it:**

1. **Rights inflate by default** — without central governance, adds outpace removes  
2. **People and orgs change constantly** — only a central process tracks HR/directory change at scale  
3. **Risk is cross-system** — one app team sees one product; attackers and abusers use combinations  
4. **Response needs a complete revoke story** — SOC can disable a login; “this person’s rights in 30 systems” must be known somewhere  
5. **Identity is the main trust boundary** — cloud, SaaS, and remote work weakened “the internal network” as the perimeter  

**Principle P5 — Identity is the primary trust boundary → standing access must be centrally governed.**  
**Principle P6 — People use IGA because rights bloat, people leave, and harm is combinatorial—not only to fill audit forms.**

Colleague-ready line:

> Firewalls and EDR handle intrusion and malware. MFA handles “who are you.” **IGA handles how many keys sit in your pocket and whether they come back when you change role or leave.** Without that layer, other security tools operate in an environment where over-privilege is normal.

---

## Core principles (checklist)

| ID | Principle |
| --- | --- |
| P1 | Authentication ≠ authorization governance |
| P2 | IGA owns standing access; many tools own “this moment” |
| P3 | Required both with and without external network attack |
| P4 | Complements detect/protect; does not replace them |
| P5 | Identity is a primary trust boundary → govern standing rights centrally |
| P6 | Adopted because rights bloat and cross-system harm—not only compliance theatre |

---

## What this doc does *not* claim

It does **not** argue that traditional IGA is enough for **AI agents** (tool/MCP calls, OBO, machine-speed revoke). That is the next chapter of this research pack (`00c`, `00d`, and `from-lab-bot/05`).  
Foundations first: **why central IGA exists in the security architecture at all.**
