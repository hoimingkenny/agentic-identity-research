# Migration map (reorg + dedupe)

Spine: see [LOGIC-CHAIN.md](./LOGIC-CHAIN.md).

| Old path | New path |
| --- | --- |
| `01-foundations-traditional-iga/*` (PR #2 draft) | `01-why-central-iga/` |
| `00d-why-traditional-security-not-enough.md` | `02-traditional-iga-limits/01-why-classic-controls-not-enough.md` |
| `from-lab-bot/08-outline-where-agentic-iga-sits.md` | `03-agentic-iga-in-ai-frameworks/01-where-agentic-iga-sits.md` |
| `from-lab-bot/06-management-1pager-scope-lock.md` | `03-agentic-iga-in-ai-frameworks/02-management-1pager.md` |
| `from-lab-bot/09-outline-agentic-iga-in-out-scope.md` | `04-agentic-iga-scope/01-in-out-shared-scope.md` |
| `00a` … `00c`, `01-ai-architecture-patterns.md` | `05-agents-architecture-operations/01`–`04` |
| `from-lab-bot/01-architecture-identity-binding.md` | `05-agents-architecture-operations/05-identity-binding-map.md` |
| `from-lab-bot/02-vendor-matrix-tier1.md` (**canonical**) | `06-vendors/01-tier1-matrix.md` |
| `02-vendor-matrix-tier1.md` (root duplicate) | `appendix/deprecated-root-vendor-matrix-tier1.md` |
| `from-lab-bot/04-okta-ping-ibm-…` | `06-vendors/02-…` |
| `06-ping-identity-for-ai-notes.md` | `06-vendors/03-…` |
| playbook (lab or root — larger kept canonical) | `07-landing/01-playbook-skeleton.md` |
| `from-lab-bot/07-shortlist-…` | `07-landing/02-…` |
| `05-architect-qa-checklist.md` | `07-landing/03-…` |
| `04-company-whitepaper-outline.md` | `07-landing/04-…` |
| `00-EXECUTIVE-SUMMARY.md` | `appendix/executive-summary.md` |
| `DISCUSSION-DIGEST.md` | `appendix/discussion-digest.md` |
| `from-lab-bot/05-macro-…` | `appendix/legacy-macro-ai-security-vs-governance.md` |
| `from-lab-bot/` folder | **removed** after move |
| flat `00a`–`06` at repo root | **removed** after move |

**Deduped:** one Tier-1 vendor matrix; one landing playbook. Old second copies under `appendix/deprecated-*` for one release, then delete.
