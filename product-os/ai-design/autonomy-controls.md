# Autonomy Controls

The per-capability autonomy dial, and how it's earned.

## The dial

Four stages, set **per capability, not per product**:

1. **Observe** — the agent reads and reasons; surfaces nothing to the user
   directly yet
2. **Suggest** — the agent surfaces a ranked hypothesis or recommendation
   (`proof-chain-ui.md`); takes no action
3. **Act-with-approval** — the agent proposes a specific change and
   requires explicit approval before executing (`approval-flows.md`)
4. **Act** — the agent executes without per-instance approval, within
   previously-approved bounds

## Promotion rule

A capability moves up one stage when its override/rejection rate drops
below a set threshold over a meaningful sample — not on a calendar.
Starting point: override rate under 20%, sustained over enough decisions to
be a real signal rather than noise on a small sample. Tune per capability
once real usage data exists; don't lock a universal number in before that
data is available.

## Per-tenant ramp

Autonomy tier is set per capability, per tenant — not globally. A new
customer starts every capability at Observe or Suggest regardless of how
far another tenant has ramped. Nothing here crosses tenant boundaries.

## Upstream

[`../ai-prd/`](../ai-prd/) — every PRD states which capabilities it
introduces and at what starting tier. [`../context-hub/`](../context-hub/).

## Status

Scaffold only.
