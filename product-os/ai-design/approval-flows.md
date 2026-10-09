# Approval Flows

How an agent asks permission to act, and how the user grants or denies it.

## The rule

An agent proposes; it never executes without approval, for any capability
below the "act" tier (see `autonomy-controls.md`). The approval card states
exactly what will change — a visible diff, not a vague "approve this
remediation" button.

## The anatomy — a verified working pattern

This exact shape is already live in Ockham's sibling customer-app product
(Hearth's Dashboard Copilot, verified end-to-end 2026-10-09): a numbered
list of the exact proposed changes → "Approve changes" / "Not now" → once
decided, the card shows who approved it and when → the next message
confirms what changed, with Undo available. Reuse this shape for
platform-app's remediation/runbook approvals rather than inventing a second
pattern — it's proven to work in this exact stack, not a hypothetical.

## Escalation path

If a proposed action sits above the current autonomy tier for that
capability, the approval flow is the only path to execution — there is no
"act anyway" override available to the agent itself.

## Upstream

[`../ai-prd/`](../ai-prd/), [`../context-hub/`](../context-hub/).

## Status

Scaffold only.
