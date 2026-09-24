# Why not let each development team manage access alone?

**Audience:** Security / IAM / engineering leads  
**Related:** [01 — Why central IGA (security view)](./01-why-central-iga-security.md)

---

## One-line answer

Access is an **enterprise risk and accountability** problem, not only an app-shipping convenience problem. Letting each development team manage access alone is fast locally and brittle globally: inconsistent rights, incomplete offboarding, and no single answer for "who has X across the firm?"

---

## What app teams own well vs poorly

**App teams are strong at:**

- Meaning of roles *inside* their product  
- How the app enforces RBAC/ABAC technically  

**They are usually weak at / not accountable for:**

1. **Cross-system consistency** — one person changes role; ten apps do not update together  
2. **Leaver / contractor end dates** — app teams are not the system of record for employment  
3. **Who approved** — an engineer adding a role ≠ business/risk approval with an audit trail  
4. **Segregation of duties** — toxic combinations often span systems; one app cannot see them  
5. **Firm-wide evidence** — every team with its own admin UI/Excel cannot answer enterprise questions  
6. **Incentives** — shipping features outranks least privilege, reviews, and orphan cleanup  
7. **Shared / service accounts** — classic outcome of fragmented ownership  

**Short split:**

- App team → **what a role means and how the app enforces it**  
- Central IGA → **who is allowed to hold that role, across systems, with proof and revoke**

Central IGA does not replace app RBAC. It governs **assignment and lifecycle** of those roles (and directory groups that apps consume).

---

## Security framing

Fragmented access admin recreates the failure mode IGA exists to prevent: **standing over-privilege with no enterprise owner**. That hurts both "quiet" hygiene failures and post-compromise blast radius.

---

## Bridge to agents

If agent credentials and tool rights are again left to each team as long-lived local secrets, the same fragmentation returns—faster. That is why this research pack treats **central IGA for humans/classic access** as foundation, then asks what extra control plane agents need.
