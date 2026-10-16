# 01 — Idea Intake: `business-insights`

**Product:** Hearth

## Problem restatement

Owners and operations directors of restaurant groups with 2–20 locations
can't get a timely, trustworthy answer to three questions: *what is happening
across my locations, why, and what should I do about it?* The data exists in
orders, menu, deliveries and payments, but no one at this size is paid to join
and interpret it. So problems surface late, typically at month-end or after a
bad review, and decisions run on instinct. Examples are a lunch delivery
bottleneck, an item that sells but loses money, or a run of failed payments.

The proposed feature is an AI business analyst inside Hearth. The **problem**
being validated is the gap between data and decisions above, not the
feature.

## Type

**Strategic bet** (Hearth horizon H2, "AI business analyst, read-only"), on
top of a **pain hypothesis** that hasn't been validated with customers yet.

## Provenance

- **Source:** the Hearth product strategy. `context-hub/hearth/product-brief.md`
  names the read-only AI analyst as horizon H2 and frames Hearth's thesis as
  "an operations console that explains itself."
- **Prior design:** the Hearth Dashboard + AI Copilot design
  (`customer-app/design_handoff/design_handoff_hearth/DASHBOARD_COPILOT.md`)
  already specifies the analyst experience: answer anatomy, premise
  correction, starter questions, and approval-gated actions.
- **Signal strength:** internal and strategic only. Hearth is pre-launch, with
  no customer interviews, tickets, usage data or sales notes. **All customer
  evidence is `[Assumed]`.**

## Existing coverage

- **`knowledge-hub/`:** empty. Nothing has shipped or been killed before.
- **In the product today:** the Copilot **interface** is built, but 100%
  mocked from a fixed script (`customer-app/services/frontend/src/copilot/copilotScript.ts`).
  There is no real data access, no LLM call and no retrieval. This is a
  surface waiting for a capability, not a partial ship of one.

## Fast fit check

- **ICP: pass.** The pain sits with the owner and operations director of a
  2–20-location, delivery-heavy US group, and "no in-house analyst" is the ICP's
  defining qualifier (`hearth/icp.md`).
- **Positioning: pass.** It is the wedge itself, "the analyst the group can't
  afford to hire" (`hearth/positioning.md`). Without it, Hearth is just another
  back-office console.

Both pass, so the idea continues to stage 02.
