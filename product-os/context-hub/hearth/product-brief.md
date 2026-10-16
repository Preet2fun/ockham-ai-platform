# Hearth — Product Brief

## Identity

- **Product:** Hearth, the second product in the Ockham portfolio.
- **Category:** Back-office operations console for multi-location restaurant
  groups.
- **Model:** B2B SaaS, multi-tenant. One tenant is one restaurant group with
  two or more locations. Pricing is `[Assumed]` per location, per month, and
  not yet decided.
- **Stage:** Pre-launch. The core console is partly built: login with MFA, the
  app shell, and the Dashboard with AI Copilot. Orders, Menu, Deliveries and
  Payments are specified but not built. There are no paying customers and no
  usage data, so customer evidence is `[Assumed]` throughout.
- **Not:** a diner-facing ordering app. There is no consumer browse-and-order
  flow.

## The problem Hearth exists for

A restaurant group's owner or operations lead runs several locations on thin
margins, often from a tablet at the counter between other tasks. Their
operational data is spread across orders, menu, deliveries and payments. Most
groups at this size have **no analyst**. "What is happening in my business,
why, and what should I do about it?" gets answered late, from gut feel, or not
at all.

## Product thesis

**An operations console that explains itself.** Hearth combines the
day-to-day operating surfaces (orders, menu, deliveries, payments) with an AI
business analyst that reads the same data and answers in plain language. The
analyst leads with the answer, shows the evidence, corrects a wrong premise,
and suggests one next step.

## Experience principles (from the shipped Dashboard + Copilot design)

- **Answer first, evidence second.** Every answer is a lead sentence, then
  supporting blocks (compare, bars, spark, list), then a next step.
- **Correct the premise.** If the user asks "why are sales down" and sales are
  up, Copilot says so before going further.
- **Never acts on its own.** Any change goes through an approval card that
  lists exactly what will change. Undo stays available afterwards.
- **Says what it doesn't know.** If a question needs data Hearth doesn't hold,
  the answer names the missing data.
- **Scoped.** Answers are limited to the signed-in tenant and the selected
  location.

## Horizons

| # | Horizon | Proves |
|---|---|---|
| **H1** | **Core operations console**: Orders, Menu, Deliveries, Payments screens on real data | Operators run day-to-day work in Hearth |
| **H2** | **AI business analyst, read-only**: grounded natural-language Q&A and a weekly business brief over the tenant's own data | Operators trust the answers and act on them themselves |
| **H3** | **Approved actions**: Copilot proposes changes (prep list, delivery quote window, menu availability, payment retry) behind the approval card | Operators trust the AI enough to let it act with approval |
| **H4** | **Forecasting and cross-location benchmarking** | Hearth plans ahead instead of only explaining the past |

H1 and H2 run in parallel. H2 can ship on today's data model, and its
read-only scope keeps the trust ramp honest. The approval-card UI already
exists in the design, but write actions are held back until H3.

## Moat

- **Tenant-native data.** The analyst reasons over the group's own
  transactional data, not a generic benchmark. That is defensible only if
  answers are verifiably grounded in it and never mix tenants.
- **Operational and analytical in one place.** The data the operator acts on
  is the data the analyst explains. There is no export, BI tool or analyst in
  between.
- **Observed by Ockham.** Hearth's application telemetry and AI telemetry
  (traces, latency, token cost, answer quality) run through the Ockham
  platform. That makes Hearth's AI unusually measurable, which feeds back into
  Hearth's quality.

## Relationship to Ockham

Hearth is a product in its own right, and it is also the **reference workload**
for the Ockham platform:

- Hearth's services emit tenant-tagged traces, metrics and logs into Ockham's
  observability stack.
- Hearth's AI features emit AI telemetry (LLM traces, latency, token
  economics, evaluation scores) into the platform's AI observability layer.
- That real application and AI telemetry is the evidence base for Ockham's
  own platform AI features. Ockham doesn't depend on synthetic data alone.

The two products share identity (one sign-in engine), the design system and
backend conventions. They do **not** share an ICP, positioning or
competitors. Each is judged against its own.
