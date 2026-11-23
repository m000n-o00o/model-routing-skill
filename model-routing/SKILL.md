---
name: model-routing
description: Route each step of a multi-step coding or research task to the right model and effort (Haiku, Sonnet, Opus, Fable) with a written contract, verify each step, escalate only on failure, and log a ledger.
---

# Model Routing

Route each step to the cheapest model and effort that passes a check. Skip this for one-step tasks.

The main session never switches model (/model rewrites the whole cache). Send each work item to a subagent with the Agent tool, setting `model` and `effort` from the contract below.

## Contract (first match wins)

| Step kind | Model @ effort |
|---|---|
| classify, tag, extract, search, read logs, summarize | haiku @ medium |
| images, screenshots, computer use | opus @ medium |
| migration, architecture, deep research, quant analysis | fable @ medium |
| scoped fix, spec-driven feature, everyday docs, repeated tasks | sonnet @ medium |
| multi-PR feature, debugging | opus @ high |
| unsure | opus @ medium |

Guard: no code edits on haiku. If a haiku item needs an edit, move it to sonnet.

## Loop

1. Write each item in TASK.md: kind, one-line goal, and a check the worker can run itself. No check, no dispatch.
2. Dispatch to the routed subagent. Independent items run in parallel.
3. Verify with a separate read-only subagent (haiku or sonnet is enough for running a check). Result: PASS or FAIL with reason.
4. On FAIL, in order: make the check runnable, raise effort to high, then move one model up (haiku, sonnet, opus, fable) at medium. Max 3 cycles, then mark the item `blocked` and tell the user.
5. Add a ledger row in TASK.md: `# | item | model | effort | cycles | result`.

## Token discipline

- Subagents return only: the result in a few lines, files changed, and the last lines of the check output (10 max). Never paste whole files or logs back into the main chat.
- Read files by line range or grep, not whole.
- Point to paths instead of re-sending content already in context.
- Long chat: if the conversation has run long (about 25 exchanges, or several large pastes or tool outputs), add one line at the end of the reply suggesting a new chat, with a short handoff block (open items, TASK.md path, last ledger rows) to paste there. Say it once per threshold, not on every reply.

## Price reference (input / output per million tokens)

Haiku 5.5 $0.10 / $0.50 (up to 100K-token prompts) - Sonnet 5.5 $2 / $10 - Opus 5.5 $4 / $20 - Fable 5.2 $8 / $40. Prices change; check before reporting exact costs.

## Final report

The verified result, the ledger table, and any blocked items with their reason. No step-by-step recap.
