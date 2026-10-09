# AI Product Strategy — Methodology

The operating rules this folder works by. Each rule below is tied to a real
Ockham doc where one exists, and left general where it doesn't.

**Status:** draft, pending review. Nothing else in `product-os/` references
this yet. Written as the review gate a future `product-strategy-agent`
(not yet built — see "How this gets used" below) would run every artifact in
this folder against before calling it done.

---

## The 6 operating philosophies

1. **Confidence over certainty.** Design strategy claims — and the product
   itself — for probabilistic outcomes, not single answers. This isn't just a
   writing style: `ai-design/`'s own `proof-chain-ui.md` already commits to
   "ranked hypotheses, not a single guess" as a product behavior. Strategy
   docs in this folder should hold themselves to the same standard — a moat
   claim or a category bet is a ranked hypothesis with a confidence level,
   not an assertion.

2. **Magic moments first.** Identify the one reasoning loop that carries most
   of the value, and sequence everything else around protecting it. For
   Ockham that loop is the agentic investigation flow — evidence-backed
   timeline, ranked hypotheses, not an empty query bar (`ai-design/README.md`'s
   `incident-workspace.md`). `product-vision.md` and `roadmap-sequencing.md`
   should name this loop explicitly and gate every horizon on whether it
   strengthens or dilutes it.

   In practice: before `roadmap-sequencing.md` commits a horizon, the
   magic-moment slice of it should be validated as a draft/prototype first —
   build only the core AI reasoning loop, not the login page or settings
   panel around it. Same discipline as root `CLAUDE.md` §10's
   Claude-Design-first rule, applied one level earlier.

   **Design for the confidence interval.** A deterministic feature is "if A
   then B"; an AI feature is "if A then likely B at X% confidence" —
   `product-vision.md` should say explicitly how the product behaves when
   the agent is uncertain, not just when it's right, and name the path from
   "demo" to "daily utility" for the magic moment specifically (adoption,
   not just capability).

3. **Context is the moat.** The sharpest, most concrete tie in this list:
   [`../context-hub/ebpf-signal-thesis.md`](../context-hub/ebpf-signal-thesis.md)
   already states Ockham's actual moat — one eBPF sensor stream feeding both
   observability and security, where competitors like Upwind have the
   security side with no APM to feed it into. `moat-thesis.md` doesn't need
   to invent a moat; it needs to formalize this one with the rigor a strategy
   doc requires (where it holds, where it erodes, what would close the gap).

   Check the moat against three competitive-advantage dimensions:
   **data moats** (proprietary data for fine-tuning/RAG — Ockham's is the
   eBPF stream itself), **agentic stickiness** (products that *do work*, not
   just show info — the autonomy-dial product bet in philosophy 5), and
   **contextual superiority** (deepest context memory of the user's domain —
   this *is* the eBPF thesis). `moat-thesis.md` should state which of the
   three the eBPF advantage satisfies and which it doesn't yet.

4. **Agentic velocity.** This one is about how the Ockham team *builds*
   strategy and product, not a customer-facing claim — use AI-agent-assisted
   workflows to draft and validate these artifacts in days, not quarters
   (this is already how `product-os/` itself has been built, folder by
   folder). No existing Ockham doc to tie this to beyond that working
   pattern — noted honestly as process, not strategy content.

5. **Ethical guardianship.** Every autonomy claim gets a visible control.
   `ai-design/README.md`'s `autonomy-controls.md` (per-capability dial:
   observe → suggest → act-with-approval → act) and `approval-flows.md`
   are the product-side half of this; `operations/`'s already-built
   responsible-AI / reliability monitoring is the ongoing half.
   `bets-and-non-goals.md` should state explicitly which autonomy level each
   horizon commits to and which it refuses.

6. **Design for the model's trajectory, not its current ceiling.** A
   capability that's too unreliable to ship today is a scoping note, not a
   permanent constraint — accuracy on a given task moves fast, and a bet
   written against this quarter's ceiling can be stale before it ships. Two
   concrete disciplines follow:

   - **Don't let today's limitation become a permanent non-goal.**
     `bets-and-non-goals.md` should distinguish a capability that's out of
     scope because it's the wrong bet (stays a non-goal regardless of model
     improvement) from one that's out of scope only because no model clears
     the accuracy bar yet (`ai-prd/`'s per-component ML-necessity check,
     addendum A) — the second kind is a revisit trigger, not a permanent no.
   - **The moat is the context, not the model — so nothing here is locked
     to one.** This is philosophy 3's direct consequence: because
     [`../context-hub/ebpf-signal-thesis.md`](../context-hub/ebpf-signal-thesis.md)
     locates Ockham's advantage in the eBPF signal stream, not in any
     specific model, swapping or upgrading the underlying model should
     strengthen a capability rather than threaten the business case for it.
     `ai-prd/`'s model-selection addendum (H) already requires a named
     fallback if pricing or availability changes — this philosophy is the
     strategic reason that requirement exists, not a new mechanism of its
     own: scope a capability's product surface (UI, autonomy dial,
     workflow) to the ceiling worth building for, not the ceiling the
     cheapest available model clears today.

   This is a different kind of "earn it" than philosophy 5's autonomy dial:
   that dial governs how much a *proven* capability is trusted with real
   usage data; this discipline governs how ambitiously a *new* capability
   gets scoped before it has any usage data at all. Don't collapse the
   two — a capability can be scoped ambitiously under this philosophy while
   still entering at Observe under philosophy 5 on day one.

---

## Anti-pattern checklist

Run every draft in this folder against this table before calling it done.

| Anti-pattern | Why it fails for Ockham specifically | Check instead |
|---|---|---|
| **Deterministic roadmaps** | A fixed roadmap can't absorb what the scale-when-green gate finds | [`../ai-launch-strategy.md`](../ai-launch-strategy.md)'s scale-or-hold call + [`../data-analysis/experiment-analysis.md`](../data-analysis/experiment-analysis.md)'s ship/iterate/kill hierarchy |
| **Silent AI failures** | An investigation that fails silently is worse than one that visibly doesn't know | `ai-design/`'s `proof-chain-ui.md` (ranked hypotheses, not a single guess) |
| **AI for AI's sake** | Sharper for Ockham than generic: [`../context-hub/competitive-landscape.md`](../context-hub/competitive-landscape.md)'s fixed 7 (Datadog, Dynatrace, Edge Delta, Resolve.ai already ship agentic AI SRE features) means matching their AI feature-for-feature isn't a strategy — the one-sensor moat is the actual reason to build any given AI feature, not the AI itself |
| **Thin context** | This is the inverse of philosophy 3 — a strategy doc written without checking `context-hub/` first is exactly the failure mode `context-hub/` exists to prevent | Root `product-os/README.md`'s own working rule: `context-hub/` is upstream of everything |
| **Ignoring data privacy** | Real, unresolved risk for a platform handling customer telemetry and security signals — no Ockham doc addresses this yet | Flag as an open item in `bets-and-non-goals.md` rather than silently assuming it's handled |

---

## Hypothesis discipline

**Observation → Hypothesis → Experiment → Validation.**

This is the missing front half of a loop whose back half is already built.
[`../data-analysis/experiment-analysis.md`](../data-analysis/experiment-analysis.md)
starts from "an experiment was run" — nothing upstream of it in `product-os/`
today defines how a strategic bet becomes a testable hypothesis in the first
place. This folder is that upstream piece:

1. **Observation** — a real signal from `context-hub/` or
   `../ai-feedback/` (not a hunch)
2. **Hypothesis** — stated in the same falsifiable shape
   `experiment-analysis.md` expects to receive: *"If we [bet], then [metric]
   moves by [amount], measured by [segment/tier from
   `../ai-gtm/icp-tiers.md`]"*
3. **Experiment** — handed off to engineering via the normal PRD path
   (`../ai-prd/`), not built directly from this folder
4. **Validation** — closes through `experiment-analysis.md`'s hierarchy and
   [`../data-analysis/calibration-log.md`](../data-analysis/calibration-log.md)'s
   predicted-vs-measured loop

A claim in `moat-thesis.md` or `category-strategy.md` that can't be phrased
in the Hypothesis shape above isn't ready to ship as strategy yet.

### Quality bar for a strategic hypothesis

Seven dimensions for judging whether a bet is ready to ship as a hypothesis,
not just an assertion:

| Criterion | Question to ask |
|---|---|
| **Testable** | Can this be checked via `../data-analysis/experiment-analysis.md`'s hierarchy, not just asserted? |
| **Falsifiable** | What specific result would prove the bet wrong — not just underperform? |
| **Parsimonious** | Does the moat/category claim need every mechanism it invokes, or would a simpler story explain the same evidence? |
| **Explanatory power** | Does it explain the fixed-7 competitors' current moves too, or only Ockham's own roadmap? |
| **Scope** | Stated for one horizon, or claimed to hold across all three (full observability → agentic SRE → AI-stack security)? |
| **Consistent** | Does it line up with what `context-hub/` already asserts, or silently contradict it? |
| **Novel** | Does it say something the fixed-7 competitors' own battlecards don't already concede? |

A hypothesis doesn't need to score well on all seven — but a strategy doc
that can't name its own weak dimensions is the "just-so story" failure mode:
a plausible narrative with no distinct, checkable prediction.

---

## Context discipline (for whoever — or whatever — drafts these artifacts)

The PM is the curator of truth for any agent working from these docs — the
same role root `product-os/README.md` already assigns `context-hub/`. Two
concrete techniques apply here:

- **Persistent context workspace** — user personas, product domain, and
  constraints already map onto real files: personas →
  [`../context-hub/icp.md`](../context-hub/icp.md), domain →
  [`../context-hub/company-brief.md`](../context-hub/company-brief.md),
  constraints → this folder's own `bets-and-non-goals.md`. Nothing new to
  build here — just confirms `context-hub/` is already shaped right.
- **Scenario triad** — for any bet in `bets-and-non-goals.md`, state the
  Happy / Struggle / Exploit path: works as intended, a confused or
  disconnected user, and a bad-faith actor probing the autonomy dial
  (philosophy 5). A bet only stated for the happy path isn't fully
  specified yet.

## How this gets used

Every one of this folder's 7 expected artifacts
(`product-vision.md`, `roadmap-sequencing.md`, `bets-and-non-goals.md`,
`moat-thesis.md`, `category-strategy.md`, `competitive-strategy.md`,
`build-vs-buy.md`) gets drafted *with* the user, one at a time, against the
philosophies and anti-pattern checklist above — not generated unsupervised.
Today that drafting has no dedicated mechanism; a `product-strategy-agent`
(same shape as `discovery-agent` / `prd-agent`) is the proposed next piece,
not yet built.
