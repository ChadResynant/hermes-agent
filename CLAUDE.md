# CLAUDE.md


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

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Governance Prerequisite (Non-Negotiable)

**Before any work in this repository, read and comply with:** [`~/repos/governance/INDEX.md`](../governance/INDEX.md)

All cross-repo contracts, policies, and enforcement gates in `~/repos/governance/` are binding.

## What This Is

Hermes agent — AI-assisted tooling for code and documentation tasks.

## Status

Active development. Taxonomy: ACTIVE_ENFORCED.
