# Security Triage View

"Was this actually anything?" — the SOC analyst's first screen on a new
detection.

## The rule

Detection, correlating application traces, and current infra state in one
place — not three tools the analyst has to cross-reference manually. This
is the screen where the eBPF one-sensor advantage
([`../context-hub/ebpf-signal-thesis.md`](../context-hub/ebpf-signal-thesis.md))
is most visible to the end user: the same stream that gives observability
also gives the security context, so this screen can show both without a
second data source.

## Severity framing

State the cost of being wrong explicitly on this screen: a false positive
here wastes analyst time; a false negative here is a missed incident.
Those are different error costs, and evidence/confidence display should be
weighted accordingly rather than treating every detection the same.

## Upstream

[`../ai-prd/`](../ai-prd/),
[`../context-hub/ebpf-signal-thesis.md`](../context-hub/ebpf-signal-thesis.md)
specifically.

## Status

Scaffold only.
