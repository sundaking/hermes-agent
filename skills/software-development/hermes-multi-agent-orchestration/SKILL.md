---
name: hermes-multi-agent-orchestration
description: Use when coordinating multi-agent implementation work in Hermes. Audit first, turn work into Kanban cards, delegate to existing worker profiles, and reconcile results before the next step.
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [hermes, orchestration, kanban, delegation, audit, reconciliation, multi-agent]
    related_skills: [kanban-orchestrator, kanban-worker, subagent-driven-development, writing-plans, requesting-code-review]
---

# Hermes Multi-Agent Orchestration

## Overview

Use this skill when a task should be coordinated as a *workflow* instead of a single turn: inspect the repo, choose the safest first move, turn that work into Kanban cards, delegate each lane to a worker profile, and reconcile the results before creating follow-up work.

Hermes stays the conductor. The workers do the implementation. Kanban is the durable handoff layer.

## When to Use

Use this skill when:

- the project already exists and needs an audit before any implementation
- more than one specialist or lane is involved
- work should survive restarts or review cycles
- tasks can run in parallel without sharing files or assumptions
- the result needs a durable audit trail

Don't use this skill for a tiny one-off change that does not need delegation. Use a direct answer or a simpler skill instead.

## Operating Model

### 1) Audit first

Start with the repo, not the task.

Record:

- branch / worktree state
- working tree cleanliness
- recent commits
- entrypoints and main modules
- tests / build / run path
- likely risks and fragile areas

The audit should end with a short brief:

- current state
- architecture snapshot
- risk snapshot
- recommendation
- first safe task
- open questions

If the project is still unclear, do another audit pass instead of guessing.

### 2) Turn the work into a Kanban packet

Before delegation, write a short packet that a worker can execute without guessing.

Include:

- goal
- repo/worktree path
- branch name or worktree name
- constraints
- expected output
- verification commands
- review expectations if needed

### 3) Create Kanban cards for delegation

Use Kanban as the actual delegation path.

Rules:

- one focused task per card
- one branch or worktree per worker
- one worker profile per lane
- use `parents` for real dependencies
- do not create dependent work as a free-floating ready card
- do not let workers share unstated assumptions

If a task has multiple independent lanes, split them into separate cards and let them run in parallel.

If a lane depends on another lane's output, create the dependency chain up front with parent links.

### 4) Use existing worker profiles

Before assigning work, confirm which profiles exist on the machine.

Common discovery path:

```bash
hermes profile list
```

If the available profiles are unknown, ask the user. Do not invent assignee names.

Typical worker routing:

- architecture / hard reasoning → a reasoning-oriented profile
- implementation / mechanical edits → a coding profile
- critique / review → a review-oriented profile
- interactive editing → a human-guided editing profile

### 5) Require worker handoff artifacts

Every worker should return:

- short summary of what changed
- diff or patch reference
- tests or checks run
- open questions or unresolved risks

If the worker output is weak or incomplete, reconcile before proceeding.

### 6) Reconcile before the next card

After a worker finishes, compare the result against the packet and card body.

Decide explicitly:

- **merge** when the work matches the packet and evidence is good
- **revise** when the work is close but fixable
- **split** when the task was too broad or mixed multiple concerns

Do not create follow-up cards until the current result is reconciled.

## Canonical Card Shape

Use a consistent structure so cards are easy to scan:

```md
# Task Packet

**Goal:**
<one-sentence outcome>

**Repo / worktree path:**
<absolute path>

**Branch name:**
<branch or worktree name>

**Constraints:**
- <constraint 1>
- <constraint 2>

**Expected output:**
- <deliverable 1>
- <deliverable 2>

**Verification commands:**
- `<command 1>`
- `<command 2>`

**Optional context:**
- **Priority:** <low/medium/high>
- **Reviewer:** <name>
- **Risk level:** <low/medium/high>
- **Relevant files:** <paths>
- **Notes:** <anything else>
```

## Recommended Workflow

1. Audit the repository.
2. Write the audit brief.
3. Create the packet.
4. Create the Kanban card(s).
5. Assign each card to an existing worker profile.
6. Wait for handoff artifacts.
7. Reconcile the result.
8. Update the brief and create the next card only if needed.

## Common Patterns

### Parallel lanes

Use when two tasks do not touch the same files:

- lane A: implementation
- lane B: docs or verification
- lane C: review

### Fan-in

Use when several lanes feed a final synthesis card:

- research card A
- research card B
- synthesis card with both parents

### Stabilize first

If the audit shows risk, start with the smallest stabilizing card instead of feature work.

## Pitfalls

- dispatching before the audit exists
- putting two independent tasks into one card
- assigning overlapping edits to multiple workers
- inventing profile names that do not exist
- treating a worker summary as proof without checking evidence
- starting follow-up work before reconciliation
- using direct ad hoc delegation when Kanban should hold the handoff

## Verification Checklist

- [ ] The repo was audited before delegation
- [ ] The brief names the first safe task
- [ ] Each card has one clear goal
- [ ] Dependencies are modeled with `parents`
- [ ] Each worker has an isolated branch or worktree
- [ ] Each worker returned summary, diff, tests, and open questions
- [ ] The result was reconciled before the next card was created
- [ ] The brief was updated with new facts

## Notes on Related Skills

- `kanban-orchestrator` and `kanban-worker` are the underlying Kanban workflow skills.
- `writing-plans` helps when the task graph needs a detailed implementation plan.
- `subagent-driven-development` helps when the work is better split into sequential implementation tasks.
- `requesting-code-review` is useful when the next step is a formal review pass.
