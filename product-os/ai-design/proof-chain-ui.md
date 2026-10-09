# Proof-Chain UI

The evidence-linked timeline pattern: how an agent shows its reasoning, not
just its conclusion.

## What it is

A ranked list of hypotheses, each backed by the specific evidence that
supports it — a trace ID, a correlating log error, a RUM latency spike —
not a single verdict with no visible work behind it. The user can trace
any hypothesis back to its source data.

## Why ranked, not single

The right answer at 70% confidence sitting next to the second-best at 20%
is more trustworthy, and more useful, than one answer stated with false
certainty — it also gives the user somewhere to go if the top hypothesis
turns out wrong, instead of a dead end.

## States to design

- **High confidence, single clear leader** — the common case
- **Multiple plausible hypotheses, close confidence** — show the
  comparison; don't force a pick the evidence doesn't support
- **No hypothesis clears a usable confidence floor** — say so plainly. Don't
  present a weak guess as if it were a strong one (`design-principles.md`)

## Used by

`incident-workspace.md` (primary consumer), `security-triage-view.md`.

## Upstream

[`../ai-prd/`](../ai-prd/) — the agent's actual confidence/evidence output
shape is defined there, this file is how it's shown, not how it's produced.
[`../context-hub/`](../context-hub/).

## Status

Scaffold only.
