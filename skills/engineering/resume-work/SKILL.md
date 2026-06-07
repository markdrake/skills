---
name: resume-work
description: Resume paused work after an interruption — end of day, emergency stop, blackout, dropped connection, crash. Given a brief of the most recent work, studies it, audits git worktrees and uncommitted local changes for half-finished work, writes a markdown continuation plan that ends in a ready-to-run agentic prompt, then shares a findings summary. Use when the user says "resume work", "pick up where we left off", "continue from the handoff", or returns after a break and wants to restart a paused task.
argument-hint: "Paste the brief of recent work, or a path to the handoff doc"
---

# Resume Work

Pick up interrupted work from a brief plus the actual state of the repo. The brief tells you the *intent*; the repo tells you the *reality*. Where they disagree, trust the repo.

Pairs with [`/handoff`](../../productivity/handoff/SKILL.md) — handoff writes the brief, resume-work consumes it.

## Input

The user provides a brief of the most recent work — pasted text, or a path/URL to a handoff doc. If no brief is given, ask for it before doing anything else. Don't guess at past work from git alone.

## Phase 1 — Understand the brief

Read the brief in full, then write down:

- **Goal** — what "done" looks like.
- **Last known state** — what was finished, what was in progress, what was untested.
- **Next intended step** — what the previous session was about to do.
- **Referenced artifacts** — PRDs, issues, ADRs, plans, branches, files. Open the ones that matter.

If the brief is vague on the goal, resolve that with the user before auditing — a wrong goal wastes the whole session.

## Phase 2 — Audit the actual state

Reconcile the brief against the repo. Run and read:

- [ ] **Branch & worktrees** — `git worktree list`, `git branch --show-current`. Are you where the brief expects?
- [ ] **Uncommitted changes** — `git status`, `git diff`, `git diff --staged`. What was touched but not committed?
- [ ] **Truncated work** — files half-edited, functions left incomplete, fresh `TODO`/`FIXME` stubs, failing or unwritten tests, dangling imports.
- [ ] **Recent commits** — `git log --oneline -10` — what actually landed vs. what the brief claims.
- [ ] **Untracked files** — `git status --porcelain` for scratch files or prototypes left behind.

Note every gap between "brief says" and "repo shows".

## Phase 3 — Summarise findings

Write a short, scannable summary: where the work actually stands, what's half-done, and any surprises (drift from the brief, broken state, merge conflicts, stale branch). This is what you'll share at the end.

## Phase 4 — Write the continuation plan

Write a markdown plan to `.scratch/resume-work/<slug>.md` (create the directory if needed; `<slug>` is a short kebab-case name for the work). Structure:

- **Goal** — restated from Phase 1.
- **Current state** — the reconciled reality from Phases 2–3.
- **Steps** — ordered, small, each independently verifiable. Reference exact files, and name the feedback loop (test or command) that proves each step done.
- **Risks / open questions** — anything needing a human decision.

Reference existing artifacts by path or URL; don't duplicate their contents.

### Append an agentic prompt

End the file with a `## Kickoff prompt` section containing a single fenced, self-contained prompt a fresh agent can run to execute the plan. It must state: the goal, the path to this plan file, the branch or worktree to work in, the first concrete action, and any skills to invoke (e.g. `/tdd`, `/diagnose`).

## Phase 5 — Share

Output the Phase 3 findings summary and the path to the plan file. Offer to start executing the first step.
