# Agentic identity research — discussion digest

**Owner:** Kenny Cheng (Hoi Ming)  
**Updated:** 2026-09-17  
**Purpose:** Capture career + research discussion so notes are readable online. Docs only — no product scaffold.

---

## 1. Career intent
- Shift from full-time general programming toward **IAM + agentic AI** (identity / automation ownership).
- Dual track: reshape role at CLSA while staying externally marketable.
- Prefer English for status/research reports; Cantonese OK in chat when preferred.

## 2. Project purpose (locked as research intent)
Research **how to land agentic identity in the enterprise**, including:
1. AI / agent **architecture** patterns  
2. Identity **problems** those architectures create  
3. Major **vendor** tooling (later)  
4. Enterprise **landing** playbook  
5. Optional thin sandbox **only if** research names a gap worth proving  

Build/scaffold remains **paused** until explicitly restarted.

## 3. Research priority (current)
**Problems first:** identify identity issues from AI agents and architecture **before** vendor selection or JIT deep-dives (HashiCorp, etc.).

See: `00a-identity-problems-from-ai-architecture.md`

## 4. Binding points (canonical)
1. Workload identity — which runtime  
2. User OBO / delegation — on whose behalf  
3. Agent-as-principal — which agent  
4. Eliminate shared service accounts  

## 5. Pack index
| File | What it is |
|------|------------|
| `00-EXECUTIVE-SUMMARY.md` | Merged executive summary |
| `00a-identity-problems-from-ai-architecture.md` | Problem catalog (current focus) |
| `01-ai-architecture-patterns.md` | Architecture → controls |
| `02-vendor-matrix-tier1.md` | Entra, Okta/Auth0, CyberArk, AWS |
| `03-landing-playbook-skeleton.md` | Phased landing outline |
| `from-lab-bot/` | Companion deeper binding tables |

## 6. Open / paused
- Lab scaffolding stopped (`唔好做住` / discussion not finished).  
- Cloud workload identity treated as important later slice, not day-one build.  
- HashiCorp: viable for JIT secrets/sessions; complements IdP, does not replace agent directory/IGA.

## 7. How to use this repo
Read `00a` first for problems, then `01` for architecture, then `02` when ready for vendors.
