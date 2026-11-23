# model-routing

A Claude skill that stops one model from doing every step of a task. It routes each step to the right model and effort, checks the result, escalates only when a check fails, and logs what each step cost.

## Why

A session pinned to one model overpays for easy steps (searching, reading logs, summarizing) or underpowers the hard ones (architecture, migrations, debugging). Switching by hand with `/model` also rewrites the prompt cache for the whole conversation. This skill keeps the main session on one model and sends each step to a subagent on the routed model.

## How it works

1. **Plan:** break the task into items, each with a one-line goal and a check the worker can run.
2. **Route:** a fixed table picks the model and effort for each item.

   | Step kind | Model @ effort |
   |---|---|
   | classify, tag, extract, search, read logs, summarize | haiku @ medium |
   | images, screenshots, computer use | opus @ medium |
   | migration, architecture, deep research, quant analysis | fable @ medium |
   | scoped fix, spec-driven feature, everyday docs | sonnet @ medium |
   | multi-PR feature, debugging | opus @ high |
   | unsure | opus @ medium |

3. **Verify:** a separate read-only subagent runs the check.
4. **Escalate on failure only:** make the check runnable, raise effort, then move one model up. Three failed cycles and the item is marked blocked.
5. **Ledger:** every step is logged in `TASK.md` with model, effort, cycles and result.

It also keeps token use down: subagents return short results instead of whole files or logs, and long chats get a one-line suggestion to continue in a new chat with a handoff summary.

## Install

**Claude (web, desktop, mobile)**
1. Download `model-routing.zip` from the Releases page, or zip the `model-routing` folder yourself.
2. In Claude, go to **Customize > Skills**, upload the zip, and turn the skill on.

**Claude Code**
Copy the `model-routing` folder into `~/.claude/skills/` (all projects) or `.claude/skills/` inside a project.

## Use

Ask for a multi-step task and mention routing, for example:

> Use model-routing: rename the config key `apiUrl` to `baseUrl` across the repo and make sure the tests pass.

Single quick questions don't need it.

## Notes

- Model names and prices reflect Claude models as of November 2026. Update the table if they change.
- Credit: based on the "Agent Model Routing" reference diagram.

## License

MIT. See [LICENSE](LICENSE).
