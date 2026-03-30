# AI-DDLC Cheat Sheet <!-- hook test 6 -->

Quick reference for the AI-Driven Development Lifecycle template.
Full context: `CONTEXT.md` · Full docs: `README.md`

---

## Slash Commands / Prompts

| Command | File | Purpose |
|---------|------|---------|
| `/refine-spec` | `.github/prompts/refine-spec.prompt.md` | Tighten `SPEC.md` with human approval gating spec edits |
| `/ralph-loop` | `.github/prompts/ralph-loop.prompt.md` | Start a Reflect → Assess → Learn → Plan → Hypothesize cycle |
| `/checkpoint-commit` | `.agents/skills/checkpoint-commit/` | Diff-based Conventional Commit during agentic work |
| `/browser-workflow` | `.github/prompts/browser-workflow.prompt.md` | Start a human-AI browser collaboration session |
| `/task` | `.github/prompts/task-template.prompt.md` | Kick off any new implementation task |

---

## Multi-Agent Protocol

```
User → Issue Intake → Orchestrator → Implementer (Claude)
                                   ↘ Reviewer (OpenAI) ←→ RALPH loop
                                   → Documenter → CONTEXT.md
```

| Agent | Preferred LLM | Role |
|-------|--------------|------|
| Issue Intake | Any | Triages issues, applies labels, routes to agents |
| Orchestrator | Any | Decomposes tasks, dispatches agents in parallel |
| Implementer | Claude | Writes and refactors code |
| Reviewer | OpenAI | Adversarial review; iterates until consensus |
| Documenter | Any | Keeps `CONTEXT.md` current |
| RALPH | Any | Drives iterative improvement loop |

---

## RALPH Loop

| Step | Action |
|------|--------|
| **R**eflect | What happened in the last cycle? |
| **A**ssess | What is the current quality / gap? |
| **L**earn | What pattern or constraint was revealed? |
| **P**lan | What is the next concrete change? |
| **H**ypothesize | What outcome do we expect, and how will we verify it? |

Use `/ralph-loop` to invoke this cycle with agent coordination.

---

## Browser Tooling — Three Tiers

| Tier | Tool | Harness support | When to use |
|------|------|----------------|-------------|
| 1 — Agent CLI | `playwright-cli` | All harnesses | Automation, DevTools reads, parallel sessions |
| 2 — DevTools MCP | `mcp_chrome-devtoo_*` | Copilot (others need config) | Human ↔ agent diagnostic handoff |
| 3 — VS Code built-in | `open_browser_page`, `fetch_webpage` | Copilot only | One-off URL fetches in VS Code chat — not for scripts |

### playwright-cli Quick Reference

```bash
# Install (local devDependency — run once from repo root)
npm install
# Invoke
npx playwright-cli <command>   # or: npm run browser -- <command>

# Core workflow
playwright-cli open https://example.com   # Open browser
playwright-cli snapshot                   # Get element refs (e1, e2, …)
playwright-cli click e3                   # Click by ref
playwright-cli fill e5 "$USERNAME"        # Fill field (always env vars for secrets)
playwright-cli press Enter
playwright-cli screenshot
playwright-cli close

# DevTools
playwright-cli console                    # Read browser console logs
playwright-cli network                    # Read network requests
playwright-cli tracing-start
playwright-cli tracing-stop

# Named sessions (parallel workflows)
playwright-cli -s=auth open https://app.example.com --persistent
playwright-cli -s=public open https://example.com
playwright-cli close-all
```

### Human ↔ Agent Collaboration

```bash
# Connect agent to YOUR running Chrome (no second browser launched)
playwright-cli open --extension https://your-app.localhost:3000

# Agent reads your browser's diagnostics
playwright-cli console    # JS console logs
playwright-cli network    # Network requests
playwright-cli snapshot   # DOM state

# You can F12 at any time — same Chrome window
```

Full decision table: `.github/instructions/browser-tooling.instructions.md`

---

## Commit Conventions

```
<type>(<scope>): <short description>
```

| Type | When |
|------|------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `refactor` | Code restructure, no behaviour change |
| `test` | Tests added or changed |
| `chore` | Tooling, config, dependencies |
| `perf` | Performance improvement |
| `ci` | CI/CD changes |

Use `/checkpoint-commit` to generate a Conventional Commit from the current diff.

---

## Naming Conventions

| Artifact | Convention | Example |
|----------|-----------|---------|
| Files | `kebab-case` | `my-feature.ts` |
| JS/TS variables & functions | `camelCase` | `handleClick` |
| Python/Go/Rust variables | `snake_case` | `user_name` |
| Classes / types | `PascalCase` | `UserProfile` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Agent files | `<name>.agent.md` | `orchestrator.agent.md` |
| Prompt files | `<name>.prompt.md` | `ralph-loop.prompt.md` |
| Instruction files | `<name>.instructions.md` | `coding-standards.instructions.md` |

---

## Key Files

| File | Purpose |
|------|---------|
| `CONTEXT.md` | Live workspace state — single source of truth for all agents |
| `SPEC.md` | Project-scoped requirements and acceptance criteria |
| `context-history.md` | Older milestone history (out of CONTEXT.md) |
| `template-spec.md` | Internal design rationale for this template scaffold |
| `AGENTS.md` | Root adapter for non-Copilot harnesses |
| `CLAUDE.md` | Root adapter for Claude-style harnesses |
| `.cursorrules` | Root adapter for Cursor-style harnesses |
| `.github/copilot-instructions.md` | Master workspace instructions for Copilot |

---

## Secrets & Safety Rules

- **Never** commit secrets, API keys, or tokens — use `.env` (git-ignored)
- Reference via environment variables: `process.env.MY_KEY` / `os.environ["MY_KEY"]`
- Never pass secrets as inline strings to browser CLI commands: use `"$ENV_VAR"` form
- Git-ignored patterns: `*.secret.md`, `.env`, `mcp.local.json`, `**/output/`, `.context7/`

