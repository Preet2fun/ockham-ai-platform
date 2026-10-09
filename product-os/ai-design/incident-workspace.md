# Incident Workspace

The responder's landing screen for an active incident.

## The rule

Opens to an evidence-backed timeline already populated
(`proof-chain-ui.md`) — not an empty query bar waiting for the responder to
start investigating from zero. The agent's reasoning work should already
be visible the moment the screen opens.

## States

- **Hypotheses ready** — the primary state: ranked, evidence-linked
  (`proof-chain-ui.md`)
- **Low confidence / insufficient signal** — say so plainly, show what data
  *is* available, don't present a weak guess as a strong one
  (`design-principles.md`)
- **Remediation available** — an `approval-flows.md` card appears inline,
  not as a separate step the responder has to go find

## Upstream

[`../ai-prd/`](../ai-prd/), [`../context-hub/`](../context-hub/).

## Status

Scaffold only.
