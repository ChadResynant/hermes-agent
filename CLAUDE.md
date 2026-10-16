# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

<!-- GOVERNANCE-PREFLIGHT-v1 -->
## Governance Pre-Flight (summary — binding rules live in governance/)

All agents — Claude, Codex, Grok, Gemini, Hermes — before starting a task:
- Follow the task-scoped startup and pre-flight requirements in the linked Agent Interaction
  Policy. Scale the plan and cited policies to the task; do not force a fixed number of steps or
  policies. Lightweight Codex work does not imply a full-context audit.
- Use the **canonical document template** for any document — do not invent a format.
- Before reporting completion, run
  `~/repos/repos-config/scripts/branch_worktree_lifecycle_gate.py --root ~/repos`; the binding
  cleanup-debt invariant is `~/repos/governance/AGENTS.md#universal-branchworktree-lifecycle-invariant`.

**AGENTS NEVER SEND (absolute order).** No email, calendar invitation, meeting update or
cancellation, or message of any kind — to a customer or to **anyone else**. **A calendar invite
with an attendee IS a message**, as is a time change, a cancellation, and any tool call with a
`notify`/`notificationLevel`/`sendUpdates` parameter. *"Set up a call with X"* authorizes
preparing the call, **not** contacting X. Produce drafts; **Chelsea Collado handles customer
communications**, Chad transmits or delegates the rest.

This is a summary; the binding rules and full checklists live in governance (source of truth):
- `~/repos/governance/policies/AGENT_INTERACTION_POLICY.md` — startup sequence + PHASE 0.5,
  and §"External Communication and Representation — Agents Do Not Transmit"
- `~/repos/claude-config/skills/prepare-cad-drawing/SKILL.md` and
  `~/repos/governance/contracts/CAD_EXPORT_FORMATS_CONTRACT.md` — engineering drawing and print
  exports, including required project/batch/date/revision/source-SHA/input-digest traceability.
- `~/repos/governance/standards/DOCUMENT_TEMPLATE_REGISTRY.md` — which template to use
- `~/repos/governance/INDEX.md` — master registry of all contracts, policies, gates
<!-- /GOVERNANCE-PREFLIGHT-v1 -->

<!-- EXECUTIVE-BREVITY-PROJECTION:BEGIN -->
## Executive Brevity Standard (generated; do not edit)

Canonical source: `governance/policies/AGENT_INTERACTION_POLICY.md#executive-brevity-standard-ebs-1`

Canonical SHA-256: `ac12a3f7444ce04b5de1c0a6bf78987eedf9e1c744c66deb12c80ac7691fe077`

Precedence (same policy, after the JSON block): WF-1 supersedes `default_sections` and
`required_no_blockers_phrase` for direct answers and ordinary completion receipts (at most
five lines); EA-1 governs executive summaries. Those two keys carry no mandatory effect, and
a later explicit instruction from Chad supersedes both.

Apply the exact canonical policy carried in this generated projection:

```json
{
  "schema": "resynant.executive_brevity.projection@1",
  "source": "governance/policies/AGENT_INTERACTION_POLICY.md#executive-brevity-standard-ebs-1",
  "canonical_sha256": "ac12a3f7444ce04b5de1c0a6bf78987eedf9e1c744c66deb12c80ac7691fe077",
  "policy": {
    "schema": "resynant.executive_brevity@1",
    "applies_to": [
      "claude",
      "codex",
      "grok",
      "antigravity",
      "hermes",
      "resy",
      "local-model",
      "botresynant",
      "orchestrator",
      "subagent",
      "future-agent-installation"
    ],
    "default": {
      "max_interactive_words": 300,
      "lead_with": [
        "decision",
        "verdict",
        "current_status"
      ],
      "report_only": [
        "blockers",
        "material_risks",
        "decisions_required",
        "next_action"
      ],
      "required_no_blockers_phrase": "No blockers. Proceed.",
      "default_sections": [
        "STATUS",
        "BLOCKERS",
        "DECISION NEEDED",
        "NEXT ACTION"
      ],
      "tables": "essential_only",
      "detail_storage": "durable_artifact",
      "internal_analysis_visibility": "does_not_expand_user_report"
    },
    "do_not_restate": [
      "accepted_architecture",
      "history",
      "accepted_evidence",
      "accepted_rationale",
      "prior_findings"
    ],
    "review_cycle_allowed_only_for": [
      "concrete_contradiction",
      "failed_invariant",
      "security_defect",
      "explicit_human_request"
    ],
    "detail_override_phrases": [
      "full report",
      "detailed analysis",
      "deep review"
    ],
    "detail_override_equivalents_allowed": true,
    "detail_override_scope": "current_task_only",
    "violation_class": "reporting_quality_defect",
    "work_validity_effect": "none"
  }
}
```
<!-- EXECUTIVE-BREVITY-PROJECTION:END -->

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

`graft/` is a generated, gitignored cache. Follow the binding [Graft Map Policy](~/repos/governance/policies/GRAFT_MAP_POLICY.md).
Before using a map, fetch the validated published copy with
`bash ~/repos/claude-config/scripts/graft_fetch_map.sh hermes-agent [harpy-user]`.
The fetcher checks clean-main provenance and the wiring digest. Compare `graft/PROVENANCE.json`'s
source commit with this repo's `HEAD`. If the source-change gap exceeds the agreed freshness threshold,
read affected source directly; if the commit or threshold is unknown, report unknown freshness
and read affected source directly. A map with missing or dirty provenance is invalid, and no
valid published map means use source directly. Contributors must not run `graft build` or
otherwise refresh maps. Use read-only queries with `--no-refresh` where supported:

```bash
graft ask "how does X work" --source --no-refresh
graft callers <Symbol.or.method> --no-refresh
graft grep "<literal>" --no-refresh
```

In Claude Code the same graph is exposed as the `mcp__graft__*` tools (load all five in one
ToolSearch); use a read-only surface after the same provenance and freshness checks.

## Local inference (Macadoo)

Hermes runs on the Sep 7, 2026 ruled path: Ollama alias `resy-qwen3.6:latest` (64K context,
tools) at `http://127.0.0.1:11434/v1` — provider `resy-ollama` in `~/.hermes/config.yaml`.
The 18088 gateway (`resy-gateway`) is chat-only for Hermes: `coder` emits tool calls as text and
`reasoner` is always grounded (tools blocked). Fleet skills are mounted under
`~/.hermes/skills/resynant/` with `skills.compact_categories: [resynant]`; keep CLI toolsets
lean and `max_tokens` small — a 32k local window has ~9k spare after the base prompt. If the
alias disappears (CCN restart evicts it), Chad re-runs
`~/repos/llm/scripts/handoff_create_resy_qwen36_alias.sh --apply`.
