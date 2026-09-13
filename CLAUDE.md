# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

<!-- GOVERNANCE-PREFLIGHT-v1 -->
## Governance Pre-Flight (summary — binding rules live in governance/)

Interactive Codex sessions use lightweight startup by default: no workspace audit or PHASE 0.5
restatement before the task is known. `codex --full-context` restores both. Other launchers retain
their configured startup behavior.

After task scope is known:
- Load only the relevant repo instructions and governance contracts.
- Use the **canonical document template** for any document — do not invent a format.

**AGENTS NEVER SEND (absolute order).** No email, calendar invitation, meeting update or
cancellation, or message of any kind — to a customer or to **anyone else**. **A calendar invite
with an attendee IS a message**, as is a time change, a cancellation, and any tool call with a
`notify`/`notificationLevel`/`sendUpdates` parameter. *"Set up a call with X"* authorizes
preparing the call, **not** contacting X. Produce drafts; **Chelsea Collado handles customer
communications**, Chad transmits or delegates the rest.

This is a summary; the binding rules and full checklists live in governance (source of truth):
- `~/repos/governance/policies/AGENT_INTERACTION_POLICY.md` — startup sequence + PHASE 0.5,
  and §"External Communication and Representation — Agents Do Not Transmit"
- `~/repos/governance/standards/DOCUMENT_TEMPLATE_REGISTRY.md` — which template to use
- `~/repos/governance/INDEX.md` — master registry of all contracts, policies, gates
<!-- /GOVERNANCE-PREFLIGHT-v1 -->

## Governance Prerequisite (Non-Negotiable)

**Before any work in this repository, read and comply with:** [`~/repos/governance/INDEX.md`](../governance/INDEX.md)

All cross-repo contracts, policies, and enforcement gates in `~/repos/governance/` are binding.

## This repo is a fork — read `AGENTS.md` first

This is Resynant's clone of the upstream open-source project
`git@github.com:NousResearch/hermes-agent.git` (remote `origin`). Upstream keeps its full
engineering guide in **`AGENTS.md`** (~1500 lines): contribution rubric, project structure,
`AIAgent` loop, CLI/TUI/gateway architecture, plugins, skills, toolsets, config rules, known
pitfalls, and testing policy. **That file is the authoritative developer guide; this file only
adds Resynant-specific rules and a short index.** Do not duplicate `AGENTS.md` content here.

Because it is a fork: do not move or rename upstream root files (`README.*.md`, `SECURITY*.md`,
`CONTRIBUTING*.md`, `AGENTS.md`) to satisfy Resynant layout rules — every upstream pull would then
conflict. Taxonomy: ACTIVE_ENFORCED.

## What Hermes Is

A personal AI agent: one agent core (`run_agent.py` → `AIAgent`) driven from a CLI (`cli.py`),
a messaging gateway (`gateway/`, ~20 platform adapters), an Ink TUI (`ui-tui/` +
`tui_gateway/`), and an Electron desktop app (`apps/desktop/`). Capability is added at the
edges — skills, plugins, service-gated tools — not by growing the core.

Two invariants govern every review (details in `AGENTS.md` §"What Hermes Is"):
- **Per-conversation prompt caching is sacred.** Never mutate past context, swap toolsets, or
  rebuild the system prompt mid-conversation (context compression is the sole exception).
- **The core is a narrow waist.** Every model tool ships on every API call. Prefer, in order:
  extend existing code → CLI command + skill → service-gated tool → plugin → MCP server → new
  core tool (last resort).

Import chain: `tools/registry.py` ← `tools/*.py` ← `model_tools.py` ← `run_agent.py` /
`cli.py` / `batch_runner.py`.

## Commands

```bash
source .venv/bin/activate            # or venv/ — see AGENTS.md §Development Environment

# Python tests — ALWAYS via the wrapper, never bare pytest (CI-parity env, xdist, per-file subprocess)
scripts/run_tests.sh                                  # full suite
scripts/run_tests.sh tests/gateway/                   # one directory
scripts/run_tests.sh tests/agent/test_foo.py::test_x  # one test
scripts/run_tests.sh -v --tb=long                     # pass-through pytest flags

# Python lint / types (pinned in pyproject [project.optional-dependencies].dev)
ruff check .
ty check

# JS workspaces (ui-tui, web, apps/desktop): root package.json fans out
npm run check                        # all workspaces
npm run fix                          # lint:fix + prettier, all workspaces
cd ui-tui && npm run dev|build|typecheck|lint|test
cd apps/desktop && npm run dev|build|typecheck|lint|test
```

Tests must not write to the real `~/.hermes/`; the wrapper gives each test a temp `HERMES_HOME`.
Tests that assert about `package.json` / `.ts` sources belong in the vitest suites, not
`tests/*.py` (the CI change classifier will otherwise skip them).

## Resynant-specific rules

- Never hardcode `~/.hermes` — use `get_hermes_home()` (`hermes_constants.py`); profiles depend on it.
- `.env` is for secrets only; behavioral settings go in `config.yaml`.
- Squash-merging a stale branch silently reverts recent upstream fixes — rebase first.
- Shared skills index: `~/repos/claude-config/skills/SKILLS_INDEX.json`.

## Navigating the code — use graft before grep

The repo is indexed by graft (a symbol/call graph with exact `file:line` spans). The index lives
in `graft/` (gitignored — run `graft build` once per clone; `graft build --deep` adds LLM
summaries). `run_agent.py` (~12k LOC) and `cli.py` (~11k LOC) are too large to read whole; ask
the graph instead:

```bash
graft ask "how does X work" --source     # ranked hits with code inlined
graft callers <Symbol.or.method>          # blast radius before a rename
graft grep "<literal>"                    # every occurrence, indexed files
```

In Claude Code the same graph is exposed as the `mcp__graft__*` tools (load all five in one
ToolSearch). Results reflect uncommitted edits.
