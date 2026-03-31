---
name: playwright-cli
description: Browser automation CLI for AI agents. Use when the user needs to interact with websites, including navigating pages, filling forms, clicking buttons, taking screenshots, testing web apps, inspecting console logs, monitoring network requests, or automating any browser task. Also use when the human shares DevTools diagnostics and wants the agent to act on them. Supports connecting to the user's real browser via extension for human-AI collaboration.
allowed-tools: Bash(npx playwright-cli:*), Bash(playwright-cli:*)
---

# Browser Automation with playwright-cli

This file is intentionally brief. The browser tooling documentation is normalized so this page does not compete with the canonical source documents.

> **Source:** [microsoft/playwright-cli](https://github.com/microsoft/playwright-cli) — maintained by the Microsoft Playwright team.

## Canonical Docs

- Architecture, tool selection, portability, and safety: `.github/instructions/browser-tooling.instructions.md`
- Human ↔ agent browser session workflow: `.github/prompts/browser-workflow.prompt.md`
- Complete `playwright-cli` command reference: `.agents/skills/playwright-cli/SKILL.md`

## Minimal Workflow

```bash
playwright-cli open https://example.com
playwright-cli snapshot
playwright-cli click e1
playwright-cli console
playwright-cli snapshot
playwright-cli close
```

Use environment variables for secrets:

```bash
playwright-cli fill e2 "$PASSWORD"
```

## Shared Browser Sessions

Use extension mode when the human and agent should share the same browser window:

```bash
playwright-cli open --extension https://your-app.localhost:3000
```

When the current harness supports DevTools or MCP diagnostics, they can augment the same shared browser session. They do not change the two-tier browser tooling model.

## Installation Preference

Prefer a project-local installation and invoke it via `npx`:

```bash
npx --no-install playwright-cli --version
```

If the local command is unavailable, follow the installation guidance in `.agents/skills/playwright-cli/SKILL.md`.

## Deep References

- [Session management](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/session-management.md)
- [Request mocking](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/request-mocking.md)
- [Storage state](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/storage-state.md)
- [Tracing](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/tracing.md)
- [Video recording](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/video-recording.md)
- [Test generation](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/test-generation.md)
- [Running code](https://github.com/microsoft/playwright-cli/blob/HEAD/skills/playwright-cli/references/running-code.md)
