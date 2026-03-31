# CONTEXT.md — Workspace State

> **Owner:** Documenter Agent (`/.github/agents/documenter.agent.md`)
> **Protocol:** This file is the single source of truth for all agents. Read it at
> the start of every session. Request an update after any meaningful change.

---

## Multi-Agent Protocol

- **Orchestrator** (`agents/orchestrator.agent.md`) decomposes tasks and dispatches agents.
- **Implementer** (`agents/implementer.agent.md`) writes code (Claude LLM preferred).
- **Reviewer** (`agents/reviewer.agent.md`) performs adversarial review (OpenAI preferred).
- **Documenter** (`agents/documenter.agent.md`) keeps `CONTEXT.md` and `context-history.md` current.
- **RALPH** (`agents/ralph.agent.md`) drives iterative improvement loops.
- **Portable skill path** (`.agents/skills/`) contains reusable capability modules for multi-harness workflows (e.g. `playwright-cli`).
- **Doc context tool** (Context7 MCP) provides on-demand access to library documentation without hallucination.

***If IDO enabled:***

- **Issue Intake** (`agents/issue-intake.agent.md`) is the **default first agent** — triages every new issue, applies labels, and routes to the correct downstream agent.

_Agents share state exclusively through `CONTEXT.md` and the workspace file tree._

---

## Project Overview
This repository provides a scaffold for AI-driven development with multiple collaborating agents. Future scope is to port it to a CLI tool to generate structure with user configurable options.

### Goals
- Provide a lightweight, language-agnostic scaffold for AI-driven development.
- Facilitate multi-agent parallelization with a clear inter-agent protocol.
- Easily adapt to any project stack.

## Core Principles

1. **CONTEXT.md is the source of truth.** Always read it before acting and request the
   documenter agent updates it after any meaningful change. Keep `CONTEXT.md` concise; store older milestone history in `context-history.md`.
2. **`SPEC.md` is the project requirements draft.** Use it for product scope, acceptance criteria, and delivery intent. Refine it with the user when requirements are incomplete or ambiguous.
3. **A user gesture is required for spec writes unless overridden with permissions.** Agents may consult `SPEC.md` by default for feature work, but they must not update it unless the user explicitly requests a spec update, invokes `/refine-spec`, or approves applying proposed spec changes.
4. **Parallelise wherever safe.** Independent sub-tasks should be dispatched to separate
   agents simultaneously. Serialise only when there is a true data dependency.
5. **Claude implements; OpenAI reviews.** Implementation agents (Claude) write or refactor
   code. Adversarial review agents (OpenAI) critique until consensus is reached.
6. **RALPH loop governs iteration.** Every significant cycle follows:
   Reflect → Assess → Learn → Plan → Hypothesize before executing.
7. **Language/framework agnostic.** All scaffolding must remain generic. No
   project-specific assumptions belong here.
8. **Secrets never leave the machine.** Never include API keys, tokens, or credentials
   in any committed file. Use `.env` (already git-ignored) or a secrets manager.

---

## Architecture Decisions

| ID | Decision | Rationale |
|----|----------|-----------|
| AD-001 | Copilot workspace files live under `.github/` | GitHub Copilot discovers workspace instructions there; keeps root clean |
| AD-002 | Claude implements, OpenAI reviews | Leverage model diversity for adversarial quality checks |
| AD-003 | RALPH loop drives iteration | Structured reflection prevents aimless cycling |
| AD-004 | `CONTEXT.md` is the inter-agent bus | Stateless agents need a shared, file-based state store |
| AD-005 | Context7 MCP for library docs | Avoids hallucinated APIs; future caching will reduce token cost |
| AD-006 | Secrets never committed | `.gitignore` blocks `.env`, `*.secret.md`, `mcp.local.json`, etc. |
| AD-007 | Issue-Driven Orchestration is the default human-team workflow | Every work unit begins as a GitHub Issue; enforced via `issue-driven-orchestration.instructions.md`, `issue-intake.agent.md`, `resolve-issue.prompt.md`, `labels.yml`, and the enhanced PR template |
| AD-008 | Shared skills live under `.agents/skills/` | Matches the multi-harness installer layout and keeps reusable skills portable across Copilot, Claude, Codex, Cursor, and similar tools |
| AD-009 | Root adapter files point back to `CONTEXT.md` | Gives non-Copilot harnesses a stable entrypoint without duplicating project guidance |
| AD-010 | Git workflow automation uses three layers | `PostToolUse` stages touched files, `checkpoint-commit` creates intentional diff-based commits, and `Stop` provides fallback autosave only when staged changes remain |
| AD-011 | Root `SPEC.md` is project-scoped | Downstream projects should refine `SPEC.md` with the user and agents, while template-maintainer design rationale lives in `template-spec.md` |
| AD-012 | Spec updates require an explicit human gesture | Agents read `SPEC.md` for feature work by default, but spec edits require `/refine-spec`, an explicit user request, or explicit approval of proposed spec changes |
| AD-013 | Two-tier browser tooling: playwright-cli + VS Code built-ins (fallback) | `playwright-cli` is the portable browser automation layer across harnesses; harness-specific DevTools or MCP diagnostics may augment shared sessions when available; VS Code built-in browser tools remain Copilot-only fallback |
| AD-014 | delete and remove references to: `browser-workflow.prompt.md`, `REFERENCE.md` artifact, `CHEAT_SHEET.md` and agent-browser skill | Replaced by the more robust and flexible `playwright-cli` workflow and consolidated README, SKILL and instruction files; see AD-013 for the new architecture |

---

## Active Work

_No tasks currently in flight. Update this section when work begins if IDO is not enabled. If IDO *is* enabled this should be managed via GitHub Issues and GitHub Projects._

| Task | Agent | Status | Branch |
|------|-------|--------|--------|
| — | — | — | — |

---

## Issue-Driven Orchestration (IDO) — Opt-in Workflow

_Not enabled by default._

**All work starts as a GitHub Issue.** No implementation begins without a filed issue
that carries clear acceptance criteria, a `type:*` label, an `agent:*` label, and a
`priority:*` label.

```
Issue filed → Issue Intake triages → agent dispatched → PR opened (Closes #N) →
Reviewer consensus → human approval → merge → Documenter updates CONTEXT.md
```

### Entry points

| You want to… | Use… |
|-------------|------|
| File new work | `.github/ISSUE_TEMPLATE/new_ticket.yaml` |
| Route an issue to an agent | `.github/prompts/resolve-issue.prompt.md` |
| Assign to Copilot coding agent | `agent:copilot` label + resolve-issue prompt |
| Run a RALPH improvement cycle | `.github/prompts/ralph-loop.prompt.md` |

### Copilot coding agent assignment

When an issue carries the `agent:copilot` label, assign it to `copilot-swe-agent`
via the GitHub web UI or the API. Pass the issue's acceptance criteria plus the
following as `customInstructions`:

```
Repository context: .github/copilot-instructions.md and CONTEXT.md.
Coding standards: .github/instructions/coding-standards.instructions.md.
IDO rules: .github/instructions/issue-driven-orchestration.instructions.md.
Open a draft PR immediately; convert to ready when all AC pass.
Commit style: Conventional Commits. Link PR with "Closes #N".
```

### TODO:

Full IDO protocol: `.github/instructions/issue-driven-orchestration.instructions.md`
Label taxonomy: `.github/labels.yml`

---

## Directory Map

| Path | Purpose |
|------|---------|
| `.github/agents/` | Agent persona definitions (`.agent.md`) |
| `.github/instructions/` | Coding standards & best-practice rules (`.instructions.md`) |
| `.github/prompts/` | Reusable task prompts (`.prompt.md`) |
| `.github/plugins/` | Bundled plugin configurations |
| `.github/scripts/` | Utility/maintenance scripts |
| `.agents/skills/` | Shared capability modules installed for multi-harness reuse |
| `SPEC.md` | Project-scoped specification draft refined by the user and agents |
| `template-spec.md` | Internal design rationale for maintaining this scaffold |
| `CONTEXT.md` | Live workspace state – updated by the documenter agent |
| `context-history.md` | Historical milestone log kept outside the session-critical context file |
| `AGENTS.md` / `CLAUDE.md` | Thin root adapters for non-Copilot harnesses |

---

## Conventions

- See [.github/instructions/coding-standards.instructions.md](.github/instructions/coding-standards.instructions.md) for full rules.

---

## Agent Roster

| Agent | File | LLM Preference | Status |
|-------|------|----------------|--------|
| Issue Intake | `.github/agents/issue-intake.agent.md` | Any | Opt-in |
| Orchestrator | `.github/agents/orchestrator.agent.md` | Any | Active |
| Implementer | `.github/agents/implementer.agent.md` | Claude | Active |
| Reviewer | `.github/agents/reviewer.agent.md` | OpenAI | Active |
| Documenter | `.github/agents/documenter.agent.md` | Any | Active |
| RALPH | `.github/agents/ralph.agent.md` | Any | Active |

---

## Open Questions

| ID | Question | Owner | Deadline |
|----|----------|-------|---------|
| OQ-001 | Context7 caching strategy — local file vs. Redis vs. SQLite? | Human | Cancelled |
| OQ-002 | Should a custom harness live inside this repo or as a separate package? | Human | Cancelled |
| OQ-003 | Which CLI agent orchestrator (if any) to add later? | Human | TBD |

---

## Recent Milestones

Older milestone history lives in `context-history.md`. Use git history when you need exact diffs, authorship, or commit-level details.

| Date | Change | Agent |
|------|--------|-------|
| 2026-03-24 | Updated the session auto-commit hook to use summarized Conventional Commits at session end without timestamps or auto-push | Copilot |
| 2026-03-25 | Implemented three-layer Git workflow automation with PostToolUse auto-stage hooks, a portable `checkpoint-commit` skill, and Stop-hook fallback autosave | Copilot |
| 2026-03-26 | Split historical milestones into `context-history.md` so `CONTEXT.md` stays compact for agent context loading | Copilot |
| 2026-03-26 | Replaced the root `SPEC.md` with a project-spec starter template and moved template-internal specification notes to `template-spec.md` | Copilot |
| 2026-03-26 | Added `/refine-spec` and documented a human-gated spec sync workflow for Orchestrator and Implementer | Copilot |
| 2026-03-29 | Replaced `agent-browser` skill with `playwright-cli`; added `browser-tooling.instructions.md`, `browser-workflow.prompt.md`, and established the browser tooling architecture captured in AD-013 | Copilot |
| 2026-03-31 | delete and remove references to: `browser-workflow.prompt.md`, `REFERENCE.md` artifact, `CHEAT_SHEET.md` and agent-browser skill | Replaced by the more robust and flexible `playwright-cli` workflow and consolidated README, SKILL and instruction files; see AD-013 for the new architecture |

---

_Last updated by: Human — agents should keep this current._
