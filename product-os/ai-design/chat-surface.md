# Chat Surface

Dashboard and notebook creation, and ad-hoc investigation, via chat.

## Status

Weakest-specified of the 7 artifacts in this folder — flagged honestly
rather than forcing false specificity. What's settled:

- A free-text question that isn't about a specific active incident lands
  here, not inside `incident-workspace.md` or `security-triage-view.md`
  directly
- The `approval-flows.md` rule still applies if a chat request would
  create or modify a dashboard/notebook, not just read one
- A response builds progressively as the agent produces it — partial
  output and its evidence render as they resolve, not held behind a
  spinner until the full answer is ready. Same transparency bar as
  `design-principles.md`'s "show confidence and cite evidence," applied to
  a surface that updates live instead of all at once.
- A follow-up in the same thread narrows or redirects the existing answer,
  carrying the conversation's established context forward rather than
  treating each message as a fresh, context-free query. The verified
  Hearth Dashboard Copilot flow (issue #54, customer-app — a sibling
  product, not this one) already demonstrates this shape end-to-end on
  mocked data: a starter question, a follow-up that narrows it, and a
  proposed change that carries that context into an `approval-flows.md`
  card. That's evidence the pattern works in practice, not a claim that
  this product's chat surface works the same way underneath.

## Open

What "notebook" means concretely in this product, and the full scope of
dashboard/notebook creation via chat, are not decided here yet.

## Upstream

[`../ai-prd/`](../ai-prd/), [`../context-hub/`](../context-hub/).
