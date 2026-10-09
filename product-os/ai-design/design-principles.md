# AI Design — Principles

Core principles governing how agent work is shown to the user, across every
screen in this folder.

## Explainable autonomy

"You can check its work." Every agent-produced claim shows its evidence
trail, not just its conclusion — see `proof-chain-ui.md` for the concrete
pattern.

## Design the failure state first

A screen's happy path — the agent is right — is the easy half. Design what
the user sees when the agent is uncertain, wrong, or has nothing confident
to say *before* designing what they see when it's right. An agent that
fails silently is worse than one that visibly doesn't know. See
`proof-chain-ui.md`'s confidence/ranked-hypothesis pattern and
`incident-workspace.md`'s low-confidence state.

## Calibrate transparency to the viewer

Show confidence and cite evidence, but calibrate *how*: a raw probability
("73% confidence") reads as precise to an engineer and as noise to a less
technical responder. `incident-workspace.md` (SRE) and
`security-triage-view.md` (SOC) are built for different audiences and
should express confidence differently — left as an open question for those
two files to resolve individually, not forced into one shared format.

## Earn autonomy, don't assume it

No screen shows an "act" control for a capability that hasn't earned it
yet — see `autonomy-controls.md` for the dial and its promotion rule.

## Upstream

[`../ai-prd/`](../ai-prd/), [`../context-hub/`](../context-hub/).

## Status

Scaffold only — first of the 7 expected artifacts in this folder.
