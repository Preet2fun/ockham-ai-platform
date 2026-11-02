# Feature SOP: Building an AI-Native Feature with Product OS

The standard operating procedure every product team member follows to take
one feature from **idea → delivery → growth**. It works for any product in
the portfolio (Ockham or Hearth). Run the steps in order. Each step names its
owner, the command, inputs, decision gates, output, and exit criteria.

---

## 0. Operating principles

1. **One feature, one slug.** Pick a kebab-case slug at intake (for example
   `business-insights`). It names every artifact, from the Discovery folder
   to the PRD, spec, plan and deployment guide, so the trail is traceable.
2. **Every run names its product.** Read that product's context in
   `context-hub/` (root files for Ockham, `context-hub/<product>/` for any
   other product). Never judge one product against another product's ICP,
   positioning or competitors.
3. **`context-hub/` is upstream of everything.** If a downstream artifact
   contradicts it, the artifact is wrong, or the hub gets updated on purpose
   and on the record.
4. **Agents do the work. Humans make the calls.** Agents run the mechanical
   stages unattended. Every judgment call stops at a gate (§10) and waits
   for the owner.
5. **Evidence over assertion.** Tag every claim `[Measured]`, `[Assumed]` or
   `[Gated]`. A missing number gets logged as a risk. Never invent it.
6. **AI-native, not AI-bolted-on.** The feature must pass the AI-native
   check (Discovery 02) and the ML-necessity check (PRD 06). Anything that
   SQL, a rule or a fixed report can answer goes to deterministic code, not
   the model.
7. **Evals are the spec.** AI quality is defined by an eval suite that gates
   CI. Demo impressions don't count.
8. **One step at a time.** Meet a step's exit criteria before starting the
   next.
9. **Gaps get routed, not absorbed.** See §11.

---

## 1. Roles

| Role | Owns |
|---|---|
| **PM** | Steps 1–2, 7–8. Gate decisions on opportunity, scope, ROI and launch. |
| **Design** | Step 3. Claude Design drafts, approval of the experience. |
| **Engineering** | Steps 4–6. Plan, build, evals, deployment. |
| **GTM** | Step 7. Messaging, plays, launch execution. |

On a small team, one person can hold several roles. The gates don't change.

---

## 2. Before you start

- [ ] **Product named:** Ockham or Hearth.
- [ ] **Slug chosen:** kebab-case, unique under `ai-discovery/discovery/`.
- [ ] **Product context read:** product brief, ICP, positioning,
      competitive landscape for that product.
- [ ] **`knowledge-hub/` checked:** what already ships that this touches or
      duplicates.
- [ ] **Feature tracker opened:** one checklist per feature run under
      `docs/superpowers/plans/` (`YYYY-MM-DD-<slug>-feature-roadmap.md`). It
      records progress through this SOP, open blockers, and parked gaps (§11).

---

## Step 1: Discovery. Should we build this?

| | |
|---|---|
| **Owner** | PM |
| **Run** | `/discovery-agent <product>: <idea in one sentence>` |
| **Input** | The idea or signal, plus product context |
| **Method** | `ai-discovery/stages/01`–`06`, `ai-discovery/opportunity-scorecard.md` |
| **Output** | `ai-discovery/discovery/<slug>/stages/*.md` and, on Pursue, `ai-discovery/discovery/<slug>/discovery-brief.md` |

| Stage | What happens | Gate |
|---|---|---|
| 01 Idea intake | Restate as a problem, check `knowledge-hub/`, check product fit | Stops only on two competing readings or a clear fit failure |
| 02 Opportunity scoring | Five factors (Magnitude, Frequency, Severity, Competition, Contrast), AI-native check, fit gates, lean | **Always stops.** Confirm the lean. |
| 03 Problem-space research | Synthesize connected signal (feedback, usage, supplied evidence) | Stops when real customer evidence is missing. Choose `[Assumed]`, wait, or carry to 05. |
| 04 Solution hypothesis | Diverge to 5+ shapes, converge to the top 1–3 | Runs clean |
| 05 Assumption & risk map | Classify every assumption | Stops on any "test before PRD" item |
| 06 Decision & brief | Pursue / Park / Kill | **Always stops.** Confirm the decision. |

**Exit criteria:** a confirmed decision. Pursue produces a Discovery Brief.
Park or Kill gets logged, and the run ends.

---

## Step 2: PRD. What exactly are we building, and how do we know it works?

| | |
|---|---|
| **Owner** | PM |
| **Run** | `/write-prd <slug>` (picks up the Discovery Brief by slug). Re-review any time with `/review-prd <slug>`. |
| **Input** | `discovery-brief.md` |
| **Method** | `ai-prd/stages/01`–`12`, `ai-prd/prd-template.md`, `ai-prd/review-rubric.md`, `ai-prd/citations.md` |
| **Output** | `ai-prd/prds/<slug>/prd.md` (with the AI-native addendum A–H) |

| Stage | What happens | Gate |
|---|---|---|
| 01 Idea brief | Adopt the brief, set the risk tier | **Stops.** Confirm the tier. |
| 02 Requirements | 2–3 full framings | **Always stops.** Pick one. |
| 03 Knowledge gathering | Reuse and impacts from `knowledge-hub/` | Runs clean |
| 04 Market & competitor research | Live check of the product's fixed competitor set | Runs clean |
| 05 Voice of customer | `/feedback-agent` lenses on connected data | Stops when real demand isn't found. The gap is logged as a risk. |
| 06 Metrics & AI design strategy | North star, guardrails, addendum A–H | **Always stops** on the ML-necessity check. FAIL means it doesn't ship as AI. |
| 07 Evidence gathering | Live queries (postgres / prometheus MCP) to tag evidence | Runs clean |
| 08 Visual strategy | Claude Design artboards | Runs clean. Review before treating as settled. |
| 09 Prototype summary | Record prototype status | Runs clean |
| 10 ROI / business case | Cost, TAM/SAM/SOM, scenarios | **Always stops.** Proceed, proceed phased, or don't. Needs real pricing. |
| 11 Draft & review | Assemble, PRD Reviewer Agent, fix, re-check | **Always stops.** Ready / Ready-with-Caveats / Not-Ready. |
| 12 Final checklist | Fixed checklist | Any waiver is the PM's call |

**Exit criteria:** the reviewer verdict is Ready or Ready-with-Caveats, the
PM accepts it, and the PRD includes at least one concrete worked eval example.

---

## Step 3: Design. What does it look like, and how is it put together?

| | |
|---|---|
| **Owner** | Design, with Engineering |
| **Run** | `superpowers:brainstorming`, with screens drafted in **Claude Design** first (root `CLAUDE.md` §10) |
| **Input** | PRD §9 (Experience & Prototype), the AI-native addendum, `ai-design/` principles (proof chain, approval flows, autonomy controls, chat surface) |
| **Output** | `docs/superpowers/specs/YYYY-MM-DD-<slug>-design.md` (experience plus technical design), with a `PRD:` link back |

The technical design must name the following:
- Service home
- Data and retrieval approach
- Tenancy and authz boundaries
- Model and provider interface
- Telemetry (span names, metrics)
- Eval plan

Design against the governing engineering guidance (root `CLAUDE.md`,
`ai-engine/CLAUDE.md`, `ai-engine/design/` pillars). Follow that guidance.
Don't edit it for one feature.

**Exit criteria:** the Claude Design draft and the written spec are both
approved.

---

## Step 4: Plan. In what order do we build it?

| | |
|---|---|
| **Owner** | Engineering |
| **Run** | `superpowers:writing-plans` |
| **Input** | The approved spec |
| **Output** | `docs/superpowers/plans/YYYY-MM-DD-<slug>.md`, with `Spec:` and `PRD:` links |

The plan breaks the work into verifiable tasks in this order:

1. Data foundation
2. Core service logic
3. Observability
4. Eval suite
5. UI wiring
6. Deployment

Each task carries its own test or verification step.

**Exit criteria:** the plan is approved.

---

## Step 5: Build. Make it, and prove its quality

| | |
|---|---|
| **Owner** | Engineering |
| **Run** | `superpowers:executing-plans` or `superpowers:subagent-driven-development`, with `superpowers:test-driven-development` per task |
| **Input** | The approved plan |
| **Output** | Working code, uncommitted, for owner review (root `CLAUDE.md` §13) |

Required in every AI-native feature:
- **Grounding:** deterministic facts come from tools (SQL, APIs). The model
  explains and reasons. It doesn't recall numbers.
- **Guardrails:** scope enforced server-side (tenant, role, read-only or
  approval level per the PRD's autonomy level).
- **Telemetry:** business spans and metrics per root `CLAUDE.md` §5, plus
  LLM traces (latency, tokens, cost per tenant) in the observability stack.
- **Evals** (per `ai-engine/design/04-evaluation.md`):
  - Offline deterministic suite: retrieval quality, answer correctness
    against ground truth, groundedness
  - Mandatory isolation/leakage tests where data is scoped
  - Adversarial and prompt-injection cases
  - Wired as a **CI gate**
  - Online scoring and human feedback once live

**Exit criteria:** all plan tasks pass verification, the eval suite passes
its thresholds in CI, and code review is clean.

---

## Step 6: Deploy. Ship it to the environment

| | |
|---|---|
| **Owner** | Engineering |
| **Run** | Per the plan's deployment tasks (Helm / ArgoCD, `ENV=dev` then `qa`) |
| **Output** | Deployment guide in `docs/product/deployment-guides/` (product/UI) or `docs/platform/deployment-guides/` (platform) |

The guide includes:
- Prerequisites
- Ordered steps
- Expected output
- Verification commands
- Rollback
- Troubleshooting
- Acceptance checklist

**Exit criteria:**
- The feature is verified live in the browser against the design draft.
- Telemetry and LLM traces are visible.
- The PRD's acceptance criteria are checked against real evidence, not
  "reviewed clean."

---

## Step 7: Launch. Put it in front of customers

| | |
|---|---|
| **Owner** | PM and GTM |
| **Run** | Score `ai-launch-strategy.md` (12-cell canvas). Then use the GTM plays as needed: `/gtm-account-research`, `/gtm-account-scoring`, `/gtm-signal-to-sequence`. |
| **Input** | PRD §13 (Rollout & Launch), `messaging.md`, `ai-gtm/` |
| **Output** | Scored launch canvas. Campaign drafts in `ai-gtm/outputs/`. |

Run `/feedback-agent launch-feedback` before launch for the risk read.

**Gates:**
- **Scale when green.** A Red on a gating cell (Customer Pain, AI
  Reliability, Go-To-Market Viability) means hold spend. It doesn't block
  shipping.
- Signal-to-sequence only drafts. It never sends. Sending is a separate,
  explicit human action.

**Exit criteria:** launch verdict recorded (scale or hold) and rollout stage
set (Alpha, Beta, GA).

---

## Step 8: Operate & grow. Keep it healthy, and learn from it

| | |
|---|---|
| **Owner** | PM and Engineering |
| **Run** | `/feedback-agent launch-feedback` (14+ days post-launch), the `operations/` frameworks, `data-analysis/experiment-analysis.md` |

| Activity | Where |
|---|---|
| Watch accuracy/groundedness, hallucination rate, latency, cost | `operations/agent-reliability-monitoring.md` |
| Log confirmed production failures | `operations/failure-triage-log.md` |
| Route customer-reported issues | `operations/feedback-routing.md` |
| Handle a live incident | `operations/incident-runbook.md` |
| Ship / iterate / kill call on measured impact | `data-analysis/experiment-analysis.md` |
| Record predicted vs measured lift | `data-analysis/calibration-log.md` |
| Record what now ships | `knowledge-hub/<slug>.md` |
| Weekly GTM housekeeping | `/gtm-weekly-update` (drafts only, always stops before writing) |

**Exit criteria (end of the run):**
- Ship / iterate / kill decision made.
- Calibration row added.
- Knowledge-hub entry written.
- Iterate sends the feature back to Step 2, or to Step 1 for a new shape.
- Recurring themes go back into Discovery as new ideas.

---

## 10. Decision gates, all in one place

| Step | Gate | Decided by |
|---|---|---|
| 1 · 02 | Opportunity lean + AI-native verdict | PM |
| 1 · 06 | Pursue / Park / Kill | PM |
| 2 · 01 | Risk tier | PM |
| 2 · 02 | Requirements framing | PM |
| 2 · 06 | ML-necessity check | PM + Engineering |
| 2 · 10 | ROI recommendation | PM |
| 2 · 11 | PRD verdict | PM |
| 3 | Design + spec approval | Design + Engineering |
| 4 | Plan approval | Engineering |
| 5 | Eval thresholds met, review clean | Engineering |
| 6 | Acceptance criteria met live | PM + Engineering |
| 7 | Scale or hold; any GTM send | PM + GTM |
| 8 | Ship / iterate / kill | PM |

Nothing moves past a gate without its owner.

---

## 11. Handling gaps found during a run

| Gap type | Example | Action |
|---|---|---|
| **SOP or Product OS gap:** a step, template or agent is missing or wrong | No agent for Step 3; a product has no GTM pack | Fix this SOP or the Product OS module, with owner agreement. Keep the change general, never feature-specific. |
| **Product/app gap:** the product can't support the feature yet | A missing screen, data field or infra component | **Park it** on that app's roadmap (GitHub Project; root `CLAUDE.md` §11) and list it under "Parked gaps" in the feature tracker. Don't build it inside the feature run unless the plan explicitly scopes it. |
| **Evidence gap:** real data doesn't exist yet | Pre-launch, so no customer feedback | Mark it `[Assumed]` and log it as a PRD risk. Never fabricate. |

---

## 12. Where everything lands

```
product-os/
  context-hub/[<product>/]                 product context (upstream)
  ai-discovery/discovery/<slug>/           Step 1: stages + discovery-brief.md
  ai-prd/prds/<slug>/prd.md                Step 2
  ai-gtm/outputs/                          Step 7: campaign drafts
  data-analysis/calibration-log.md         Step 8
  knowledge-hub/<slug>.md                  Step 8
docs/superpowers/specs/YYYY-MM-DD-<slug>-design.md         Step 3
docs/superpowers/plans/YYYY-MM-DD-<slug>.md                Step 4
docs/superpowers/plans/YYYY-MM-DD-<slug>-feature-roadmap.md  run tracker
docs/{product,platform}/deployment-guides/                 Step 6
```
