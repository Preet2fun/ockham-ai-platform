# Ockham

[![CI Build](https://github.com/preet2fun/ockham-ai-platform/actions/workflows/ci-build.yml/badge.svg)](https://github.com/preet2fun/ockham-ai-platform/actions/workflows/ci-build.yml)
[![CI Lint](https://github.com/preet2fun/ockham-ai-platform/actions/workflows/ci-lint.yml/badge.svg)](https://github.com/preet2fun/ockham-ai-platform/actions/workflows/ci-lint.yml)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active%20development-brightgreen.svg)](#project-status)

**A unified observability + security operations platform, built AI-native from
day one — and proven against a real multi-tenant SaaS app instead of
synthetic data.**

Most observability tools and most security tools are bought, staffed, and
operated separately — two budgets, two on-call rotations, two consoles that
don't talk to each other. Ockham's thesis is that for a team that owns both
uptime *and* security without a dedicated SOC, that split is the actual
problem. This repo is the reference implementation: an agentic layer that
investigates an incident across telemetry, security signals, and change
history, and hands back an evidence-backed hypothesis — not a black-box
answer, not an empty query bar.

> Started as a single-tenant ITSM demo (hence the git history). Reframed
> around the AI-native observability/security thesis above — see
> [`product-os/`](product-os/) for the product strategy and messaging this
> engineering repo is building toward.

---

## Table of contents

- [How this repo is organized](#how-this-repo-is-organized)
- [Architecture at a glance](#architecture-at-a-glance)
- [Technology stack](#technology-stack)
- [Repository structure](#repository-structure)
- [Getting started](#getting-started)
- [Documentation map](#documentation-map)
- [Project status](#project-status)
- [Contributing](#contributing)
- [License](#license)

---

## How this repo is organized

Three engineering projects, one purpose — plus the product-strategy side that
decides what they build next.

| | [`customer-app/`](customer-app/) — **"Hearth"** | [`platform-app/`](platform-app/) — **"Synap"** | [`ai-engine/`](ai-engine/) |
|---|---|---|---|
| **Role** | The thing being observed | The thing doing the observing | The actual point of this repo |
| **What it is** | A multi-tenant restaurant/food-delivery SaaS app. Deliberately polyglot (Go, Python, Java) and deliberately simple. | The platform: owns identity for *both* apps (its `user-service` is the only identity engine in the system) and runs the full observability stack. | The agentic AI layer. Consumes customer-app's tenant-tagged telemetry, flowing through platform-app's observability stack, to run agentic investigations. |
| **Job** | Generate real, tenant-scoped, production-like activity — orders, menus, deliveries, payments. | Collect that activity (OTel → Prometheus/Loki/Jaeger → Grafana), enforce multi-tenant isolation (Istio + OPA), and GitOps-deploy both apps (ArgoCD). | Do the RCA — e.g. "why did this fail, and is it an attack" — across SRE, ITSM, and Security tracks, then write what it learns back to memory for next time. |
| **Status** | Login + MFA and the App Shell / Dashboard / AI-Copilot UI are built (mocked copilot); backend services have CRUD, DB migrations, and OTel instrumentation. | Identity, the observability stack, Istio/OPA tenant isolation, and ArgoCD GitOps are in place. | 5-pillar design is written (orchestration, memory, tools, eval, agent skills); the service itself is still a stub. |

[`product-os/`](product-os/) is the **"what and why"** that feeds the
engineering roadmap above: company context, positioning, PRDs, discovery, and
GTM materials for Ockham as a product — not part of the running system, and
not tracked the same way (see its own [README](product-os/README.md)).

---

## Architecture at a glance

```
  Real traffic & orders              Tenant-tagged OTel telemetry           Agentic investigation
      customer-app          ───────▶        platform-app          ───────▶      ai-engine
       ("Hearth")                            ("Synap")                      (the actual point)
  the thing being observed              the thing doing the observing      RCA + memory recall
```

Both apps share one identity engine and one Postgres instance, but keep
separate tenant schemas — `customer_a/b/c` for customer-app, legacy
`tenant_a/b/c` for platform-app. Every authenticated request carries
`X-Tenant-ID` / `X-User-Role` headers injected by Istio from the JWT; no
service re-validates the token itself. Full detail:
[`platform-app/docs/platform/03_Multi_Tenancy.md`](platform-app/docs/platform/03_Multi_Tenancy.md).

**Request flow, either app** (Istio handles this identically for both):

```
Browser
  │
  ▼  host: tenant-a.itsm.local (dev)  /  qa-tenant-a.itsm.local (qa)
Istio IngressGateway
  │
  ├─ RequestAuthentication  — validates JWT via JWKS (platform-app's user-service)
  ├─ outputClaimToHeaders   — injects X-Tenant-ID + X-User-Role from JWT claims
  ├─ AuthorizationPolicy    — tenant isolation (ALLOW/DENY)
  └─ OPA ext_authz          — RBAC: role + HTTP method + path, via Rego
  │
  ▼  VirtualService routes to the right namespace
Services (no JWT parsing, no authz logic — just read the two headers)
  │
  ▼  OTLP
OTel Collector ──▶ Prometheus / Loki / Jaeger ──▶ Grafana
```

---

## Technology stack

**platform-app ("Synap"):**

| Service | Language | Framework | Key libs |
|---|---|---|---|
| User Service | Go 1.22+ | Chi v5 | `pgx/v5`, `golang-jwt/jwt v5`, `otelchi` |
| Notification Service | Go 1.22+ | Chi v5 | `pgx/v5`, `amqp091-go` |
| Asset / Incident Services | Python 3.12+ | FastAPI 0.111+ | SQLAlchemy 2.x async, asyncpg |
| AI Service | Python 3.12+ | FastAPI 0.111+ | pgvector |
| Frontend ("Synap UI") | TypeScript | Vite + React 18 | React Router, TanStack Query, Zustand, CSS Modules |

**customer-app ("Hearth"):**

| Service | Language | Framework |
|---|---|---|
| order-service | Go 1.22+ | Chi v5 |
| catalog-service | Python 3.12+ | FastAPI 0.111+ |
| delivery-service / payment-service | Java | Spring Boot |
| Frontend ("Hearth") | TypeScript | Vite + React 18 — React Router, TanStack Query, Zustand, `lucide-react` |

**ai-engine:** Python / FastAPI, LangGraph + Langfuse only, CI-gated online +
offline evals — see [`ai-engine/CLAUDE.md`](ai-engine/CLAUDE.md).

**Shared infrastructure:**

| Layer | Technology |
|---|---|
| Database | PostgreSQL 17.5 — one external standalone instance, shared by both apps |
| Cache | Redis 7.x — one instance per app |
| Queue | RabbitMQ 3.13.x (platform-app only, today) |
| Service mesh | Istio (demo profile) — mTLS, JWT validation, tenant routing |
| Policy engine | OPA 0.65+ with Envoy plugin — RBAC via Rego |
| GitOps | ArgoCD 2.11+ |
| Kubernetes | kubeadm 1.29+, 3 nodes |
| Observability | OTel Collector → Prometheus + Loki + Jaeger → Grafana |

No API Gateway service, no Docker Compose, no Postgres StatefulSet — Istio
and an external Postgres instance cover those jobs. Full rationale in
[`.claude/CLAUDE.md`](.claude/CLAUDE.md) §3.

---

## Repository structure

```
ockham-ai-platform/
├── customer-app/         "Hearth" — the multi-tenant SaaS app being observed
│   ├── services/         order, catalog, delivery, payment + frontend
│   ├── database/         customer_tenants registry + per-tenant schema migrations
│   ├── infra/            Helm + K8s manifests
│   ├── design_handoff/   Claude Design prototypes — source of truth for the UI
│   └── docs/             deployment guides, tenant-isolation evidence
├── platform-app/         "Synap" — identity + the observability stack
│   ├── services/         user, asset, incident, notification, ai-service (stub) + frontend
│   ├── infra/argocd/     ArgoCD Application manifests (dev + qa)
│   ├── design_handoff_synap/  Claude Design prototypes for Synap UI
│   └── docs/             architecture, deployment guides, k8s/OTel concept docs
├── ai-engine/            the agentic AI layer — design docs + a stub FastAPI service
│   └── design/           5-pillar design: orchestration, memory, tools, eval, agent skills
├── product-os/           product strategy: context, positioning, PRDs, discovery, GTM
├── docs/superpowers/     design history — specs/ (chronological) + plans/ (implementation)
├── scripts/              GitHub Projects roadmap automation
└── .github/              CI workflows (build, lint, docker push) + issue templates
```

---

## Getting started

### Prerequisites

| Requirement | Version | Notes |
|---|---|---|
| Kubernetes (kubeadm) | 1.29+ | 3-node cluster, already installed |
| Helm | 3.15+ | `brew install helm` |
| kubectl | 1.29+ | pointed at your cluster |
| Istio CLI (`istioctl`) | 1.21+ | `brew install istioctl` |
| Docker | 20+ | for local image builds |
| ArgoCD CLI | 2.11+ | `brew install argocd` |
| OPA CLI | 0.65+ | `brew install opa` |

### Clone

```bash
git clone https://github.com/<your-username>/ockham-ai-platform.git
cd ockham-ai-platform
```

### Bring up platform-app (identity + observability stack)

```bash
cd platform-app

# Add local DNS entries (run once)
echo "$(kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}') tenant-a.itsm.local tenant-b.itsm.local tenant-c.itsm.local" | sudo tee -a /etc/hosts

ENV=dev bash scripts/setup-cluster.sh    # Istio, OPA, ArgoCD, tenants, observability
ENV=dev bash scripts/seed-data.sh        # demo data
bash scripts/port-forward.sh             # port-forward Grafana / Jaeger / ArgoCD / Prometheus
```

Full step-by-step guide, including the `qa` environment and troubleshooting:
[`platform-app/docs/platform/deployment-guides/`](platform-app/docs/platform/deployment-guides/).

### Bring up customer-app (the observed SaaS app)

```bash
cd customer-app
bash scripts/create-customer-tenants.sh
bash scripts/run-migrations.sh
```

Full guide: [`customer-app/docs/deployment-guide.md`](customer-app/docs/deployment-guide.md).

### Environment switching (dev / qa)

Every script, Helm chart, and ArgoCD Application accepts `ENV=dev` (default)
or `ENV=qa` — switching environments never requires a manual YAML edit:

```bash
ENV=qa bash scripts/setup-cluster.sh
```

See [`platform-app/docs/platform/deployment/03_Environment_Guide.md`](platform-app/docs/platform/deployment/03_Environment_Guide.md).

---

## Documentation map

| Topic | Where |
|---|---|
| Platform architecture overview | [`platform-app/docs/00_Overview.md`](platform-app/docs/00_Overview.md) |
| Service design | [`platform-app/docs/platform/02_Service_Design.md`](platform-app/docs/platform/02_Service_Design.md) |
| Multi-tenancy model | [`platform-app/docs/platform/03_Multi_Tenancy.md`](platform-app/docs/platform/03_Multi_Tenancy.md) |
| AI platform architecture | [`platform-app/docs/platform/07_AI_Platform_Architecture.md`](platform-app/docs/platform/07_AI_Platform_Architecture.md) |
| OTel / observability concepts | [`platform-app/docs/platform/observability/`](platform-app/docs/platform/observability/) |
| Istio, OPA, HPA concepts | [`platform-app/docs/platform/k8s-concepts/`](platform-app/docs/platform/k8s-concepts/) |
| customer-app deployment + tenant isolation evidence | [`customer-app/docs/`](customer-app/docs/) |
| Hearth UI design source of truth | [`customer-app/design_handoff/`](customer-app/design_handoff/) |
| ai-engine's 5-pillar design | [`ai-engine/design/`](ai-engine/design/) |
| Product strategy, positioning, PRDs | [`product-os/`](product-os/) |
| Design history (why things look the way they do) | [`docs/superpowers/specs/`](docs/superpowers/specs/) (chronological) |
| Implementation plans | [`docs/superpowers/plans/`](docs/superpowers/plans/) |

---

## Project status

Task-by-task delivery status lives in the
**[GitHub Project](https://github.com/Preet2fun/ockham-ai-platform/issues)**,
not in this README — a status table here would just drift out of sync with
the board. See [How this repo is organized](#how-this-repo-is-organized)
above for the current high-level state of each project.

---

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for branch naming, commit
conventions, and the PR process, and [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)
for community expectations. Found a security issue? See
[`SECURITY.md`](SECURITY.md) — please don't open a public issue for it.

---

## License

Apache License 2.0 — see [`LICENSE`](LICENSE).
