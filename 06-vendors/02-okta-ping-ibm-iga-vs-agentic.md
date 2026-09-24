# Okta vs Ping Identity vs IBM — Traditional IGA vs Agentic Identity

**Audience:** Kenny (SailPoint IIQ engineer, ~1yr; finance/enterprise)  
**Research date:** 2026-09-22 (HKT)  
**Scope:** Docs/primary sources only. Mark **GA / EA / Preview / Research / Marketing** carefully.  
**Baseline:** SailPoint IIQ (and ISC where noted) as Kenny’s IGA system of record.

---

## 1. Executive contrast table

| Vendor | Traditional IGA strength | Agentic strength (as of 2026-09-22) | Best fit | Biggest gap |
| --- | --- | --- | --- | --- |
| **Okta** | **Moderate (IAM-first).** Workforce Identity + Identity Governance: access request, certifications, lifecycle for Okta/SCIM-connected apps. Not SailPoint-class SoD/entitlement depth over hybrid/legacy. | **Strongest shipped agent IdP story among the three.** Okta for AI Agents **GA (~2026-04-30)**; Auth0 for AI Agents **GA (2025-11)**; XAA/ID-JAG + Auth for MCP **GA**; A2A Connections **GA**; Agent Gateway still **Research**; Resource Access Certs for Agents **EA**. | Finance orgs that already run Okta Workforce as IdP and need a vendor-neutral **agent identity + runtime PEP** layer beside IIQ. | Traditional IGA depth vs IIQ; Agent Gateway not GA; agent certifications still EA; OIG coverage limited by Okta-connected/SCIM reach. |
| **Ping Identity** | **Moderate–good on AM; lighter native IGA.** PingOne / AIC / PingFederate / PingDirectory strong for AM/federation/CIAM/workforce. PingOne Identity Governance exists (requests, certs, SoD, JML). Deep IGA often via **SailPoint partnership**, not replace-IIQ. | **Strongest runtime-first agent stack that is GA.** Identity for AI **GA by 2026-03-31** (Agent IAM Core + Agent Gateway + Agent Detection). May-2026 Agent Discovery/Governance + Agent Privilege announced; availability caveats. | Orgs already on Ping (esp. AIC/ForgeRock lineage) needing **token-exchange OBO + MCP gateway PEP** now; complements IIQ rather than replacing it. | Not a SailPoint-class IGA SoR; Agent Governance/Privilege packaging vs shipped SKUs needs AE confirmation; less “shadow agent discovery” marketing maturity than Okta. |
| **IBM** | **Strongest traditional IGA of the three.** IBM Verify Identity Governance (IVIG; ISIM/ISVG lineage) — lifecycle, compliance, SoD (incl. SAP-oriented), analytics. Closer to SailPoint-class IGA than Okta/Ping native IGA. | **Earliest / vapor-risk for production agentic.** IBM Agent Identity **Public Preview (~2026-09-01)** in Verify; watsonx Orchestrate Agent Identity **private preview (~2026-09-21)**. Docs exist; **not GA**. | IBM-centric estates (Verify + Vault + watsonx Orchestrate) willing to EAP; identity-fabric story with Entra/Okta for humans. | Agentic not production-ready; preview APIs/UI evolving; finance production kill-switch/audit chain not yet a buy decision. |

---

## 2. Per-vendor detail (with citations)

### 2.1 Okta

#### Traditional IGA / IAM posture

- **Historically IAM/IdP-first:** SSO, MFA, Universal Directory, Lifecycle Management, OIN connectors. Governance is an **add-on layer**, not the original product DNA.
- **Okta Identity Governance (OIG):** access requests, access certifications, governance workflows integrated with Workforce Identity Cloud ([Identity Governance product](https://www.okta.com/products/identity-governance/); [help center IGA](https://help.okta.com/OIE/en-us/content/topics/identity-governance/iga.htm)).
- **Honest depth vs SailPoint IIQ:** OIG is credible for **SaaS-heavy, Okta-connected (esp. SCIM)** estates. It does **not** match IIQ on entitlement-level certification at scale, mature SoD policy packs (SAP/Oracle ERP), role mining, or hybrid/legacy connector breadth. Common pattern in finance: **Okta = authentication/SSO; SailPoint = IGA SoR**.

#### Agentic capabilities (status matters)

| Capability | Status (as of research date) | Cite |
| --- | --- | --- |
| **Okta for AI Agents / Govern Agentic Identity** | **GA** — product pages state GA; investor/blog point to availability **~2026-04-30** (blog dated 29 Apr 2026) | [Product](https://www.okta.com/products/govern-ai-agent-identity/); [GA blog](https://www.okta.com/en-ca/blog/ai/okta-for-ai-agents-general-availability/); [Investor PR blueprint](https://investor.okta.com/news-and-events/news-releases/news-details/2026/Okta-Announces-New-Blueprint-for-the-Secure-Agentic-Enterprise/default.aspx) |
| Discover / register agents + human owner in Universal Directory | **GA** (part of Okta for AI Agents) | Same |
| Shadow AI discovery (OAuth consent grants; managed Chrome) | **GA** (browser expansion “coming soon”) | GA blog |
| Kill switch / agent deactivation + Universal Logout for agents | **GA** | Product FAQ + investor PR |
| Access request / certify agent access | Core workflows **GA**; **Resource Access Certifications for AI Agents** = **Early Access** (Jul 2026) | [Jul 2026 newsroom](https://www.okta.com/newsroom/articles/okta-july-2026-product-innovations/) |
| Short-lived tokens; MCP servers as governed resources; secrets/service accounts | **GA** (resource connection types in GA blog) | GA blog |
| **Cross App Access (XAA) / ID-JAG** | **Shipped** Okta implementation of IETF identity-assertion grant; developer guides live | [XAA token exchange guide](https://developer.okta.com/docs/guides/xaa-request-token-ex/main/) |
| **Auth0 for AI Agents** | **GA 2025-11-19** (Token Vault, CIBA async auth, FGA for RAG) | [Auth0 GA blog](https://auth0.com/blog/auth0-for-ai-agents-generally-available/) |
| **Auth for MCP** (Auth0) | **GA** (exited EA; industry reporting ~2026-05) | [Auth0 Auth for MCP GA](https://auth0.com/blog/auth0-auth-for-mcp-servers-generally-available/) |
| **Agent as Principal / Agent SSO** | Framed under Okta for AI Agents / XAA agent identity (treat agents as first-class principals) | Product + XAA docs |
| **Agent Gateway** (runtime identity-native MCP/tool proxy) | **Research release** — request via Research Partner Program; **not GA** as of Jul 2026 newsroom | [Agent Gateway blog](https://www.okta.com/blog/product-innovation/agent-gateway-runtime-governance/); Jul 2026 newsroom |
| Agent-to-Agent Connections | **GA** (Jul 2026) | Jul 2026 newsroom |
| FedRAMP/HIPAA SKU caveats | Separate “Okta for AI Agents - Core” for regulated; some features (ISPM, Privileged Access, some secret connections) **not yet** in regulated SKU | Product page footnote |

**Runtime vs governance split (Okta):** Governance/identity plane (directory, ownership, certifications, kill switch) is largely **GA**. Deep **runtime PEP** for arbitrary third-party agents without code changes = **Agent Gateway (Research)**. XAA/ID-JAG + Auth0 MCP are the standards-based path available now for apps that implement the exchange.

#### vs SailPoint IIQ/ISC (one-liner)

> Okta complements IIQ as IdP + emerging **agent control plane**; it does **not** replace IIQ for finance-grade entitlement SoD/cert campaigns over hybrid apps.

---

### 2.2 Ping Identity (PingOne / PingFederate / PingDirectory / AIC–ForgeRock lineage)

#### Traditional IGA / IAM posture

- **Core strength = access management + federation + CIAM/workforce**, not classic on-prem IGA boxes like IIQ.
- Stack: **PingOne**, **PingOne Advanced Identity Cloud (AIC)** (ForgeRock lineage), **PingFederate**, **PingDirectory**, DaVinci orchestration, PingOne Protect.
- **PingOne Identity Governance:** access requests, certifications/micro-certs, SoD, lifecycle signals/provisioning, AI-assisted review ([capability page](https://www.pingidentity.com/en/capability/identity-governance.html); AIC IGA docs exist).
- **Market reality:** Many enterprises keep **SailPoint for deep IGA** and Ping for AM; Ping marketplace still lists SailPoint IdentityNow connectors — partnership model, not “Ping = IIQ replacement.”

#### Agentic capabilities

| Capability | Status | Cite |
| --- | --- | --- |
| **Identity for AI** umbrella | **GA globally by 2026-03-31** | [Press 2026-03-24](https://press.pingidentity.com/2026-03-24-Ping-Identity-Defines-the-Runtime-Identity-Standard-for-Autonomous-AI); [dev release notes](https://developer.pingidentity.com/identity-for-ai/release-notes/idai-whats-new.html) |
| **Agent IAM Core** | **GA** (solution package; required for PingOne AI agent features) | [Product](https://www.pingidentity.com/en/product/agent-iam-core.html); [PingOne AI Agents docs](https://docs.pingidentity.com/pingone/ai_agents/p1_ai_agents.html) |
| First-class agent identity, owners, enable/disable lifecycle | **GA** (docs) | PingOne AI Agents docs |
| OAuth 2.0 token exchange OBO (`act` / `may_act`); autonomous client-credentials flows | **GA** (docs + configure guides) | Same + AIC configure autonomous/OBO flows |
| HITL via CIBA | **GA** (docs) | PingOne AI Agents docs |
| **Agent Gateway** (PingGateway MCP filters: audit, protection, validation) | **GA** with Identity for AI | Press + release notes |
| **Agent Detection** (PingOne Protect — CUA / agentic traffic) | **GA** via Protect | Press + release notes |
| Agent Discovery & Governance (lifecycle, ownership, audit) | Announced **2026-05-27**; press notes *“certain capabilities subject to availability”* — treat as **shipped-or-rolling; verify SKU with AE** | [Press 2026-05-27](https://press.pingidentity.com/2026-05-27-Ping-Identity-Redefines-the-Identity-Control-Plane-for-the-Agentic-Enterprise) |
| **Agent Privilege** (JIT access for desktop/coding agents; secrets not exposed to agent) | Same May-2026 announcement; product page exists — **confirm GA vs limited availability** | [Agent Privilege](https://www.pingidentity.com/en/product/agent-privilege.html); May press |
| “Agentic IAM” messaging | Productized as **Identity for AI / Agent IAM Core** — **not vapor** for Core/Gateway/Detection; Governance/Privilege need availability check | Above |

**Marketing vs shipped:** Agent IAM Core + Agent Gateway + Agent Detection are the clearest **GA** triad. Broader “Agent Governance / Privilege / programmable identity” May messaging is directionally real but **availability-caveated** — do not treat entire May deck as finance-production without AE confirmation.

#### vs SailPoint IIQ/ISC (one-liner)

> Ping is an **AM/runtime peer** (and optional lighter IGA); keep IIQ as SoR for certifications/SoD — use Ping Agent IAM for **delegation + MCP PEP**, not as IIQ replacement.

---

### 2.3 IBM

#### Traditional IGA / IAM posture

- **IBM Verify** family: workforce/consumer IAM (SSO, MFA, adaptive access, orchestration) plus **IBM Verify Identity Governance (IVIG)**.
- **IVIG** is the modern IGA product line consolidating legacy **ISIM / ISVG (IGI)** — lifecycle provisioning, compliance, activity-based SoD, analytics ([product](https://www.ibm.com/products/verify-identity-governance); [docs overview](https://www.ibm.com/docs/en/sig-and-i/11.0.0?topic=overview-verify-identity-governance)). Migration from ISIM 10.x → IVIG 11 is a **new deploy**, not in-place.
- **vs Okta/Ping traditional:** IBM still fields a **real IGA platform** in the SailPoint/Saviynt competitive set for enterprises that stayed on IBM stack. Depth is historically strong in regulated/mainframe-adjacent estates; operational cost and modernization path vary.

#### Agentic capabilities

| Capability | Status | Cite |
| --- | --- | --- |
| **IBM Agent Identity** (Verify portfolio) | **Public Preview** announced ~**2026-09-01** (after private preview). Explicitly: APIs/UI evolving ahead of GA | [Community blog Public Preview](https://community.ibm.com/community/user/blogs/dinesh-jain/2026/09/01/ibm-agent-identity-public-preview); [docs](https://www.ibm.com/docs/en/agent-identity?topic=overview) (publish meta ~2026-09-02) |
| Discover/register agents, ownership, OAuth access model, audit, risk/remediation | **Preview** | Same |
| Token exchange / OBO patterns with Verify | Documented architecture pattern | [Developer article](https://developer.ibm.com/articles/securing-ai-agents/) |
| Integration: watsonx Orchestrate + **IBM Vault (HashiCorp)** + **Verify Antenna** sync | Architecture claimed in preview | Public Preview blog |
| **Agent Identity in watsonx Orchestrate** | **Private preview** published **2026-09-21**; IdP integrations Verify or Entra | [IBM News](https://www.ibm.com/new/announcements/announcing-the-private-preview-of-agent-identity-in-ibm-watsonx-orchestrate) |
| GA agentic kill switch / enterprise cert campaigns for agents | **Not claimed as GA** | Preview language |

**Verdict:** IBM has a coherent **identity fabric** story (agent registry + Vault secrets + orchestrate runtime) but as of **2026-09-22** this is **preview / EAP**, not a finance production buy for the agentic layer alone.

#### vs SailPoint IIQ/ISC (one-liner)

> IVIG can compete with IIQ as traditional IGA **if already IBM**; Agent Identity is **preview only** — do not displace IIQ or bet production agent controls on it yet.

---

## 3. Same problem lens: managing agents effectively

Problem split Kenny should use in design reviews:

| Concern | What “good” looks like | Okta | Ping | IBM |
| --- | --- | --- | --- | --- |
| **Owner + inventory** | Every agent has human owner; discoverable registry | **GA** Universal Directory + shadow discovery | **GA** Agent IAM Core owners; Discovery/Governance expanding | **Preview** Agent Registry |
| **Lifecycle (JML for agents)** | Onboard → review → disable/decommission | **GA** workflows + kill switch | **GA** enable/disable; broader lifecycle gov rolling | **Preview** lifecycle & govern pillar |
| **Certification / SoD** | Periodic prove-access; toxic combo detection | Agent access certs **GA-ish**; Resource Access Certs **EA**; SoD still weaker than IIQ | Ping IGA SoD is human/app-centric; agent cert depth TBD | Traditional IVIG strong for **humans**; agent certs not GA |
| **Runtime PEP / tool auth** | Every tool call authorized, short-lived, attributed | XAA/ID-JAG + Auth0 MCP **GA**; **Agent Gateway Research** | **Agent Gateway GA** (PingGateway MCP filters) | Preview OBO via Verify; Vault as RS |
| **OBO / delegation chain** | `sub` + `act` (no impersonation) | XAA/ID-JAG + OBO patterns | Token exchange with `act`/`may_act` **documented GA** | OBO via RFC 8693 in architecture articles; preview product |
| **Kill switch** | Instant revoke / stop new tokens | **GA** agent deactivation | Disable agent + gateway terminate/throttle | Preview remediation via Antenna/Vault/Orchestrate |
| **Audit chain** | Who → which agent → which tool → which policy | Telemetry to SIEM (GA claims); Gateway adds attribution when available | Gateway audit filters + `act` claim (must configure mapping) | Preview audit linking human/agent/policy/credential |

**Practical takeaway:** For **owner/lifecycle/cert**, keep **IIQ (or ISC)** as the governance SoR and feed agent identities in as a new identity class when connectors exist — or certify in the agent IdP and reconcile. For **runtime PEP/delegation/audit**, Okta (**GA identity + Research gateway**) and Ping (**GA gateway**) are ahead of IBM (**preview**).

---

## 4. Recommendation posture — finance org that already has IIQ

**Do not replace IIQ** with Okta OIG, Ping IGA, or IBM IVIG “because agents.” Agents do not erase SAP SoD, entitlement certification, or hybrid connectors.

| Posture | When |
| --- | --- |
| **Buy / complement — Okta agentic layer** | Already Okta Workforce IdP; need GA agent directory, kill switch, XAA/MCP auth; can wait on Agent Gateway Research or use XAA-native paths. Keep IIQ for human + classic NHI IGA. |
| **Buy / complement — Ping Identity for AI** | Already PingFederate/AIC/PingOne; need **GA Agent Gateway + token-exchange OBO now**. Pair with IIQ. Confirm Agent Privilege/Governance SKU availability. |
| **Ignore / watch — IBM Agent Identity** | Unless heavy Verify + watsonx + Vault and EAP appetite. Re-evaluate at **GA**. IVIG only if IBM IGA strategy already exists (rare if IIQ is SoR). |
| **Ignore as IIQ replacement** | All three for traditional deep IGA in a bank/broker that already standardized on SailPoint. |

**Suggested architecture for Kenny’s peers:**

1. **IIQ** = SoR for human identities, roles, SoD, access certs, classic service accounts.  
2. **Existing IdP** (often Okta or Ping) = SSO + MFA + (now) **agent principals**.  
3. **Agent runtime PEP** = Okta XAA/Auth0 MCP and/or Ping Agent Gateway (prefer GA components).  
4. Sync/reconcile agent inventory + ownership into IIQ when a connector/process exists; until then, dual inventory with clear owner of record.  
5. Kill switch runbook: IdP disable agent + revoke tokens + gateway block — map to SOC playbooks.

---

## 5. Citation index (primary)

| Topic | URL | Date / note |
| --- | --- | --- |
| Okta for AI Agents product | https://www.okta.com/products/govern-ai-agent-identity/ | Retrieved 2026-09-22; claims GA |
| Okta for AI Agents GA blog | https://www.okta.com/en-ca/blog/ai/okta-for-ai-agents-general-availability/ | 2026-04-29 |
| Okta Secure Agentic Enterprise PR | https://investor.okta.com/news-and-events/news-releases/news-details/2026/Okta-Announces-New-Blueprint-for-the-Secure-Agentic-Enterprise/default.aspx | GA ~2026-04-30 |
| Okta Agent Gateway blog | https://www.okta.com/blog/product-innovation/agent-gateway-runtime-governance/ | Research release |
| Okta Jul 2026 innovations | https://www.okta.com/newsroom/articles/okta-july-2026-product-innovations/ | Gateway Research; A2A GA; Resource Certs EA |
| Okta XAA guide | https://developer.okta.com/docs/guides/xaa-request-token-ex/main/ | Live docs |
| Auth0 for AI Agents GA | https://auth0.com/blog/auth0-for-ai-agents-generally-available/ | 2025-11-19 |
| Auth0 Auth for MCP GA | https://auth0.com/blog/auth0-auth-for-mcp-servers-generally-available/ | ~2026-05 |
| Okta Identity Governance | https://www.okta.com/products/identity-governance/ | Traditional IGA add-on |
| Ping Identity for AI GA press | https://press.pingidentity.com/2026-03-24-Ping-Identity-Defines-the-Runtime-Identity-Standard-for-Autonomous-AI | GA by 2026-03-31 |
| Ping Identity for AI release notes | https://developer.pingidentity.com/identity-for-ai/release-notes/idai-whats-new.html | Mar 31 2026 GA entry |
| Ping Agent IAM Core | https://www.pingidentity.com/en/product/agent-iam-core.html | Product |
| PingOne AI Agents docs | https://docs.pingidentity.com/pingone/ai_agents/p1_ai_agents.html | Requires Agent IAM Core package |
| Ping May 2026 control plane press | https://press.pingidentity.com/2026-05-27-Ping-Identity-Redefines-the-Identity-Control-Plane-for-the-Agentic-Enterprise | Availability caveats |
| Ping Identity Governance | https://www.pingidentity.com/en/capability/identity-governance.html | Traditional IGA capability |
| IBM Agent Identity Public Preview | https://community.ibm.com/community/user/blogs/dinesh-jain/2026/09/01/ibm-agent-identity-public-preview | ~2026-09-01 |
| IBM Agent Identity docs | https://www.ibm.com/docs/en/agent-identity?topic=overview | Preview docs |
| watsonx Orchestrate Agent Identity | https://www.ibm.com/new/announcements/announcing-the-private-preview-of-agent-identity-in-ibm-watsonx-orchestrate | 2026-09-21 private preview |
| IBM Verify Identity Governance | https://www.ibm.com/products/verify-identity-governance | Traditional IGA |
| IBM securing AI agents article | https://developer.ibm.com/articles/securing-ai-agents/ | Architecture pattern |

---

## 6. Evidence gaps / watch list

1. **Okta Agent Gateway → GA date** still open (Research as of Jul 2026).  
2. **Okta Resource Access Certifications for AI Agents** — EA maturity and finance audit acceptance unknown.  
3. **Ping Agent Privilege / Agent Discovery & Governance** — May press has availability caveats; need AE/SKU confirmation vs marketing.  
4. **IIQ connectors** to Okta/Ping/IBM agent directories — not verified in this pass; critical for SoR design.  
5. **IBM Agent Identity GA timeline** — public preview only; no firm GA date found.  
6. **FedRAMP/HIPAA** gaps on Okta for AI Agents Core SKU — relevant for some finance affiliates.  
7. Independent bake-off data (latency, token volume, MCP filter behavior) — not in vendor docs; lab needed.

---

*File generated for parent summarization (Cantonese). English source of truth.*
