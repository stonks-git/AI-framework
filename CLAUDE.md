# CLAUDE.md

This file is the single source of truth for this project. Rules, charter, roadmap, and session state live here. When autocompact occurs, Claude re-reads this file and continues from where it left off.

---

## RULES

### 1. Contract

Human owns **WHAT** and **WHY**. AI owns **HOW**.

- Never silently change the human's goals, scope, or priorities.
- Propose alternatives, don't impose them.
- If a decision changes WHAT or WHY, get explicit human approval first.

### 2. State as Truth

**This file is authoritative, not the conversation.**

- When in doubt, re-read CLAUDE.md.
- After autocompact, trust this file over any residual memory.
- All task status, decisions, and context live here.

### 3. Anti-Drift

Do the task. Only the task. Nothing else.

- No scope creep. No "while I'm here" improvements.
- No opportunistic refactoring of surrounding code.
- No adding features, tests, types, or docs that weren't requested.
- If you notice something worth fixing, note it in ROADMAP as a new task. Don't fix it now.

### 4. Anti-Hallucination

Every claim needs evidence. No exceptions.

- Reference code as `file_path:line_number`, never copy blocks into this file.
- If you're not sure, mark it `[ASSUMED]` with reasoning.
- Never present assumptions as facts.
- If you can't find evidence, say so. Don't fabricate.

### 5. Verification-First

A task is NOT done until verification passes.

- Every task in ROADMAP must have a `verify` field (command, test, or check).
- Flow: **execute -> verify -> pass? document + mark done : fix and retry**
- Document what was done and what verification confirmed, inline in the task.
- If verification fails, task stays `doing` with failure notes.

### 6. Consequence Mapping

Before any change, think through effects:

- **1st order**: What files/functions change directly?
- **2nd order**: What breaks, what imports/calls are affected?
- **3rd order**: What user-facing behavior or UX changes?

If 2nd or 3rd order effects are non-trivial, flag to human before proceeding.

### 7. SOTA Check

For architectural decisions or technology choices:

- WebSearch current best practices (2026).
- Compare your approach to state of the art.
- Cite sources in decisions: `[SOTA: source]`.
- Don't rely on training data alone for evolving topics.

### 8. Dependency DAG

Tasks have dependencies. Respect them.

- A task can only start if all `depends_on` tasks are `done`.
- Before starting work, check which tasks are ready (deps satisfied, status = `todo`).
- If you detect a dependency cycle, flag it immediately. Don't try to work around it.
- Execute tasks in topological order: highest priority first among ready tasks.

### 9. Decomposition Trigger

If a task looks like it will take >30 minutes or touch >3 files: **stop and split it** into smaller atomic tasks before executing. Each sub-task must have its own `verify` field.

### 10. Escalation Protocol

Stop and ask the human when:

- Requirements are ambiguous and you'd be guessing.
- The change has irreversible consequences (data deletion, production deploy).
- You've tried 2-3 approaches and none work.
- The task requires domain knowledge you don't have.
- You're uncertain whether the scope is correct.

Don't spin. Ask early.

### 11. Memory Markers & Context Pressure

**Memory Marker**: After each task, write a marker in HANDOFF:
```
MEMORY_MARKER: <timestamp> | <last_task_completed> | <next_task>
```

**Autocompact detection**: If you don't remember writing the most recent memory marker, autocompact happened. Re-read this entire file before doing anything.

**Context pressure**: When conversation gets long (many tool calls, large outputs):
- Document more aggressively in ROADMAP task notes.
- Update HANDOFF proactively, don't wait for task completion.
- If you sense context is heavy, write state NOW, not later.

### 12. Knowledge Base Protocol

Project knowledge lives in `KB/` as modular files:
- `KB/KB_index.md` — table of contents, always read first.
- `KB/KB_001_title.md`, `KB/KB_002_title.md`, etc. — individual chapters.

**When to update KB:**
- After any task that changes architecture or reveals non-obvious behavior.
- When you discover a pattern, gotcha, edge case, or design decision worth preserving.
- Reference KB entries from ROADMAP task notes when relevant.

**KB keeps context window light**: only load the chapters you need, not the whole KB.

### 13. Auditor Agents

Specialized subagents for deep analysis. Invoke when needed, or when the human requests an audit.

| Agent | Use When |
|-------|----------|
| `audit-security` | Code handles user input, auth, forms, sensitive data. **Run first on any audit.** |
| `audit-archeologist` | Codebase structure unclear, before major refactoring, finding dead code. |
| `audit-database` | Schema issues, missing indexes, N+1 queries, data integrity problems. |
| `audit-code-quality` | God objects, duplication, poor naming, missing error handling. |
| `audit-frontend` | Responsive issues, accessibility, touch targets, inconsistent UI. |
| `audit-ops` | Deployment, backups, SSL, monitoring, logging, disaster recovery. |
| `audit-prospector` | **Before implementing any new feature** — finds existing patterns to follow. |

**Audit orchestration stays in the main session.** You decide what to audit, in what order, and interpret findings directly. Don't delegate the orchestration.

**SOTA protocol for auditors**: Every finding must include a WebSearch for current best practices. Compare current code to SOTA. Cite sources.

### 14. Documentation Discipline

After each completed task:
1. Update the task entry in ROADMAP (status, notes, what verification confirmed).
2. Update HANDOFF (memory marker, current state).
3. If the task revealed something worth preserving: update KB.

**Slow and precise > fast with gaps.** Don't batch documentation for later. Do it immediately after each task.

---

## CHARTER

> Fill this section once at project start. It defines WHAT and WHY.

### Project

```
name:
one_liner:
type:                   # web_app | cli | api | library | mobile | other
why:                    # Why does this project exist? What problem does it solve?
```

### Stack

```
language:
framework:
database:
hosting:
key_dependencies:       # List major libs/services
```

### Success Criteria

> How do we know this project is done? List measurable outcomes.

```
- [ ]
- [ ]
- [ ]
```

### Constraints

```
time:                   # Deadline or time budget
budget:                 # Cost constraints
must_use:               # Technologies/patterns that are required
must_avoid:             # Technologies/patterns that are forbidden
```

### Architecture Decisions

> Record key decisions here with reasoning. Format:
> `D-001: <decision>` — <reasoning> `[SOTA: source]`

```
```

### Project-Specific Rules

> Rules that apply only to this project. Examples:
> - "Production server is READ-ONLY"
> - "All API endpoints must have rate limiting"
> - "Mobile and desktop share no code"

```
```

### Deploy Policy

```
environment:            # local | staging | production
deploy_command:
pre_deploy_checks:      # What must pass before deploy
rollback_procedure:
```

---

## ROADMAP

> Generated from Plan Mode after brainstorming session with human.
> Each task is atomic with explicit verification.

### How to Read This Roadmap

- Tasks are ordered by dependency (topological sort).
- Pick the first task where: `status = todo` AND all `depends_on` are `done`.
- After completing a task: fill `notes` and update `status` right here.

### Archive Policy

When a plan is fully complete (all tasks `done` or `skipped`):
1. Move the entire Tasks section to `ROADMAP_archive.md` with a timestamp header.
2. Clear the Tasks section here back to empty.
3. Decisions stay — they're permanent project context.

This keeps CLAUDE.md light for the next plan. Archive is read-only reference, never edited after move.

### Decisions

> Format: `D-XXX: <decision>` | status: proposed/accepted/rejected | reasoning

```
```

### Tasks

> Format per task:

```
#### TASK-XXX: <title>
- status: todo | doing | done | blocked | skipped
- priority: P0-P3 (P0 = critical)
- depends_on: [TASK-YYY, TASK-ZZZ]
- deliverable: <what artifact is produced>
- verify: <command or check that proves it works>
- notes: <filled after completion — what was done, what verification confirmed, evidence as file:line>
```

<!-- TASKS START — do not remove this marker -->

> No tasks yet. Enter Plan Mode to brainstorm and generate the roadmap.

<!-- TASKS END — do not remove this marker -->

---

## HANDOFF

> Updated after EACH completed task. This is your recovery point after autocompact.

### Last Session

```
date:
summary:
```

### Current State

```
in_progress:            # Task currently being worked on (if any)
next_task:              # Next task to pick up
blockers:               # Anything preventing progress
questions:              # Open questions for the human
```

### Memory Marker

```
MEMORY_MARKER: <timestamp> | <last_task_completed> | <next_task>
```

> If you don't remember writing this marker, autocompact happened.
> Re-read this entire file before doing anything else.
