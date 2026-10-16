# Business Insights (customer-app RAG analytics) — Feature Roadmap

Living checklist. Tick items as they complete; add detail under a phase when
that phase starts. Not committed by Claude (root CLAUDE.md §13).
Slug used across Product OS, spec, and plan: **`business-insights`**.

## Decisions on record (2026-10-13)

| # | Decision |
|---|---|
| Track | New customer-app **"Business Insights"** track (SRE/ITSM/Security remain the only ai-engine example tracks). **Nothing is written into `ai-engine/` docs** — ai-engine is design/best-practice guidance only; follow it, don't edit it. Record this decision here + in the spec, not in `ai-engine/CLAUDE.md`. |
| Goal | RAG per industry best practice, with quality measured by evals |
| Scope v1 | Natural-language Q&A over a tenant's own business + a weekly insight digest |
| Retrieval | Numeric questions → SQL tools; context/semantic → pgvector. Embed menu items, order text, and daily/weekly rollup summaries |
| Vector store | pgvector (0.8.0 available on PG 17.5, not yet installed) |
| Tenancy | Strictly per-tenant (customer_a/b/c). Retrieval scoped server-side from `X-Tenant-ID`, never client input |
| Access | `admin` role only. No platform-staff view (avoid #47) |
| Autonomy | Read-only (L0–L1): answers and recommends, takes no actions |
| Observability | Self-hosted Langfuse **v3** on a separate 8 GB / 8-core machine, deployed platform-side; traces, latency, token economics, per-tenant tags |
| Data | Synthetic ~90-day backfill for all 3 tenants, matching live service shapes; designed together at that stage |
| LLM / embedding model | **Deferred** — decide before phase 5 (keep behind a provider interface) |
| Process | Full Product OS lifecycle, no skipped stages (also validates Product OS) |
| Portfolio | Hearth (customer-app) is a **second product** in the portfolio with its own ICP/positioning/competitors; the feature is evaluated as a Hearth feature. Hearth is also the reference workload whose app + AI telemetry later drives Ockham platform AI features |
| Service home | Insights code = isolated module inside `ai-service` (read-only DB role on `customer_*`, no platform-app imports) |
| Langfuse host | Separate 8 GB / 8-core machine; Docker Compose allowed **for this VM only** (out-of-repo deployment, documented as a guide) |
| Doc voice | Product OS docs read as real PM artifacts — no session narration ("user chose…", "in session…") |
| Tracking | This file (local). Mirror to GitHub issues only if/when asked |

## Open items / blockers

- [ ] LLM provider + budget (needed before phase 5)
- [ ] Embedding model (needed before phase 4 embedding work)
- [ ] Langfuse machine: IP, OS, access method (needed before phase 6)
- [ ] `CREATE EXTENSION vector` permission for the `itsm` DB user (verify in phase 4)
- [ ] Customer evidence is pre-launch → `[Assumed]` in Discovery/PRD; log as PRD risk
- [ ] `ai-gtm/pricing-and-packaging.md` is thin → blocks PRD stage 10 ROI (TODO.md item 2)

## Phases

### 0. Portfolio setup — Hearth as a second product in Product OS
- [x] Multi-product `context-hub/` structure + agent product-selection rule (design approved)
- [x] Hearth product brief (incl. Hearth → Ockham reference-workload link)
- [x] Hearth ICP (2–20 locations, owner + ops director, delivery-heavy QSR/casual, US)
- [x] Hearth positioning ("analyst-in-the-console" wedge)
- [x] Hearth fixed competitor set (Toast, Square, Restaurant365, Olo, Tenzo, status quo) — facts unverified until stage 03

### 1. Discovery (`/discovery-agent`) — stops at stages 02 and 06
- [x] Run loop; slug `business-insights`, product Hearth — stage 01 done (fit pass)
- [x] Stage 02 drafted — lean Pursue (awaiting confirmation)
- [ ] Opportunity lean confirmed (02)
- [ ] Pursue/Park/Kill decision (06) → `discovery-brief.md`

### 2. PRD (`/write-prd`, `/review-prd`) — stops at 01, 02, 05, 06, 10, 11
- [ ] PRD drafted, incl. AI-native addendum A–H, concrete worked eval example
- [ ] ML-necessity check passed
- [ ] Reviewer verdict: Ready / Ready-with-Caveats

### 3. Design (brainstorming + Claude Design, per CLAUDE.md §10)
- [ ] Extend Hearth Copilot screen for real answers (constrained to existing answer-block types)
- [ ] Written spec in `docs/superpowers/specs/`, approved
- [ ] Implementation plan (`writing-plans`), approved

### 4. Data foundation
- [ ] Install pgvector; migration for per-tenant embedding tables (verify live schema first)
- [ ] Synthetic data generator (~90 days, 3 tenants, realistic skew/anomalies for eval ground truth)
- [ ] Chunking + embedding pipeline, tenant-scoped, idempotent refresh

### 5. Insights service (LangGraph)
- [ ] Graph: route → SQL tools / vector retrieval → grounded answer with citations
- [ ] Tenant scoping + read-only guardrails; spans `customer.insights.*` with `tenant.id`/`user.role`
- [ ] Weekly digest job

### 6. Langfuse v3 (platform side)
- [ ] Dedicated machine provisioned
- [ ] Deploy (Postgres, ClickHouse, Redis, S3-compatible, web, worker)
- [ ] Traces, latency, token cost per tenant; dashboards

### 7. Evals
- [ ] Offline deterministic suite (retrieval hit-rate, answer correctness vs SQL ground truth, groundedness)
- [ ] Cross-tenant leakage test (mandatory, blocking)
- [ ] Adversarial/injection cases; CI gate
- [ ] Online scoring in Langfuse + human feedback

### 8. Hearth wiring
- [ ] Replace `copilotScript.ts` mock with real backend; verify in browser vs design

### 9. Operations
- [ ] Deployment guide (`docs/product/deployment-guides/`)
- [ ] `operations/agent-reliability-monitoring.md` signals wired
- [ ] `/feedback-agent launch-feedback`; calibration-log row
