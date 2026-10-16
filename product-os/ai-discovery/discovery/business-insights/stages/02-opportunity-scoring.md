# 02 — Opportunity Scoring: `business-insights`

**Product:** Hearth

**Evidence basis:** Hearth has no feedback data connected and no usage
telemetry, so `product-os/data-analysis/impact-estimation.md` cannot ground
Magnitude or Frequency numerically yet. Every factor is scored from judgement
and marked **`[Assumed]`**. Competitor statements come from
`context-hub/hearth/competitive-landscape.md` and are **unverified** until
stage 03.

## Part A — The five factors

| Factor | Score | Reason | AI angle |
|---|---|---|---|
| **Magnitude** | 4 `[Assumed]` | Every group in the ICP (2–20 locations, no analyst) has the gap between data and decisions. It applies across delivery-heavy formats. No market count is cited until `data-analysis/` holds a sourced number. | Horizontal across the ICP: the same questions (sales, delivery, menu, payments) apply in every tenant. |
| **Frequency** | 5 | Operational questions come up daily, and every order, delivery and payment adds new data. | Constant fresh data per tenant to reason over, and a weekly brief every week. |
| **Severity** | 3 `[Assumed]` | Costs margin (unprofitable items or channels), reputation (late deliveries) and cash (payment leakage). Usually a slow bleed, not an outage. | High cognitive load: explaining *why* means joining several data domains and spotting patterns a busy operator won't. |
| **Competition** | 3 `[Assumed, unverified]` | POS and back-office incumbents offer reporting, and some are understood to be adding AI assistants. "Why, plus a next step" over **joined** order, delivery and payment data for groups this size appears less well served. To be verified in stage 03. | Current answers are limited by the operator's own hours: reports exist, interpretation doesn't. |
| **Contrast** | 3 `[Assumed]` | The expected complaint is "I have reports, not answers." There is no customer voice yet to confirm it. | Operators want interpretation and a next step, not more dashboards. |

**Read:** 4 / 5 / 3 / 3 / 3, all 3 or above.

## Part B — The AI-native check

**Verdict: Native for the "why" layer. Bolt-on risk is explicit.**

- **Bolt-on (no model needed):**
  - KPI tiles, fixed reports, and single-number questions such as "what were
    sales yesterday?" are SQL.
  - Shipping a chat box that only rephrases fixed reports would be a garnish.
- **Native:**
  - **Explaining *why*** across domains, for example: deliveries late
    → lunch spike → prep time up on two items. That needs multi-step reasoning
    over joined, messy data.
  - **Correcting a wrong premise.**
  - **Answering open-ended questions** the product team didn't anticipate.
  - **Ranking what matters this week** across many signals for the weekly
    brief.

  An analyst does all of this today, and this ICP doesn't have one.
- **Implication for the PRD:** the value metric must be measured on "why" and
  open-ended questions. Deterministic lookups go straight to SQL as tools, not
  through the model's own recall.

## Part C — Hearth fit (product-resolved per `context-hub/README.md`)

| Check | Result | Reason |
|---|---|---|
| **ICP** | Pass | The pain sits with the owner and operations director. "No analyst" is the ICP's core qualifier. |
| **Positioning** | Pass | It *is* the wedge, "the operations console that explains itself." |
| **Moat** | Partial | The moat holds only if answers are verifiably grounded in tenant data and never mix tenants, and if Hearth's AI telemetry through Ockham makes quality measurable. **Risk:** a POS incumbent already holds the same transactional data and can add the same assistant. The defensibility is quality and joined data, not data access. |
| **Horizon** | Pass, with a dependency | H2 runs in parallel with H1 (`hearth/product-brief.md`). Dependency: the H1 screens (Orders, Menu, Deliveries, Payments) aren't built, so "View affected orders" style follow-throughs have nowhere to land yet. |

## Lean

**Pursue.** All factors score 3 or above, the check is Native for the "why"
layer, and every fit gate passes, with the moat partial. The single biggest
reason is that this capability *is* Hearth's positioning wedge. Without it,
Hearth has no differentiation.

**Carry forward to stages 03–05:**

1. Is the gap between data and decisions real and painful for the ICP? This is
   wholly `[Assumed]` today.
2. How far do Toast and the others already go on "explain my business"?
3. Does today's data model hold enough to answer the "why" questions? The
   Copilot design expects per-item prep time, delivery ETA versus actual,
   payment failure reasons, and forecast inputs.
4. Moat risk from incumbents who already hold the POS data.
