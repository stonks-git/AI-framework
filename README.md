# AI Framework

A lightweight framework for AI-assisted software development. Drop it into any project and get structured, repeatable results from any LLM.

## What's Inside

**`CLAUDE.md`** — The brain. A single file that governs how AI works on your project: rules, charter, roadmap, and session state. It survives context resets and keeps the AI on track across long sessions.

**`.agents/`** — Specialized auditor prompts you can feed to any LLM:

| Agent | What it does |
|-------|-------------|
| `audit-security` | Finds vulnerabilities — SQLi, XSS, CSRF, hardcoded secrets |
| `audit-archeologist` | Maps business logic, finds dead code, proposes clean architecture |
| `audit-database` | Schema issues, missing indexes, N+1 queries, data integrity |
| `audit-code-quality` | God objects, duplication, naming, error handling |
| `audit-frontend` | Responsive issues, accessibility, touch targets, UI consistency |
| `audit-ops` | Deployment, backups, SSL, monitoring, disaster recovery |
| `audit-prospector` | Finds existing patterns before you write new code |

**`KB/`** — A modular knowledge base that grows with your project. Keeps context light by loading only what's needed.

## How to Use

1. Copy the framework file into your project root
2. Fill in the **Charter** section (project name, stack, goals)
3. Copy `.agents/` for auditor capabilities
4. Start a session — the AI reads the file and follows the rules

## LLM Compatibility

The framework works with any LLM CLI that reads a project-level instruction file. Just use the right filename:

| LLM | File | CLI |
|-----|------|-----|
| Claude | `CLAUDE.md` | [Claude Code](https://docs.anthropic.com/en/docs/claude-code) |
| Gemini | `GEMINI.md` | [Gemini CLI](https://github.com/google-gemini/gemini-cli) |
| ChatGPT | `CODEX.md` | [Codex CLI](https://github.com/openai/codex) |
| *Other* | *Check your CLI's docs* | *Most follow the same pattern* |

The content is identical — only the filename changes. Copy `CLAUDE.md`, rename it, and you're set.

## Philosophy

- **Human owns WHAT and WHY. AI owns HOW.**
- Every claim needs evidence. No hallucinations.
- Do the task. Only the task. No scope creep.
- A task isn't done until verification passes.
