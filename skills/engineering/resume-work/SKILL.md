---
name: resume-work
description: Resume paused work after an interruption — end of day, emergency stop, blackout, dropped connection, crash. Given a brief of the most recent work, studies it, audits git worktrees and uncommitted local changes for half-finished work, writes a markdown continuation plan that ends in a ready-to-run agentic prompt, then shares a findings summary and STOPS — it produces the plan and never executes it. Use when the user says "resume work", "pick up where we left off", "continue from the handoff", or returns after a break and wants to restart a paused task.
argument-hint: "Paste the brief of recent work, or a path to the handoff doc"
---

# Resume Work

Pick up interrupted work from a brief plus the actual state of the repo. The brief tells you the *intent*; the repo tells you the *reality*. Where they disagree, trust the repo.

Pairs with [`/handoff`](../../productivity/handoff/SKILL.md) — handoff writes the brief, resume-work consumes it.

## This skill is PLAN-ONLY — it never executes

`resume-work` finishes when the plan is written and the findings are shared. **It does not implement, edit, build, install, refresh a worktree, or run any part of the plan.** The plan *is* the deliverable; producing it is the whole job. Phases 1–5 below build the plan and then stop.

- **Write the plan, hand it over, and STOP.** Execution is a *separate* task the user starts explicitly and in their own words ("go", "execute step 1", "build it"). Nothing in this skill — and no answer the user gives you — authorizes you to begin building.
- **A clarifying question is NOT a gate to execute.** You may have to ask questions to *write* the plan (see Input and Phase 1). The user's answer authorizes only the narrow detail you asked about — never the build. Asking "how should I verify?" and getting an answer is *not* permission to edit code, touch a worktree, or compile. Never use a question to manufacture a green light or to bypass the user's authority.
- **Surface the plan BEFORE you ask anything not strictly needed to write it.** The user can't approve what they can't see. Show the plan (or the part of it that prompts the question) first; ask after — so the user always knows what they'd be approving.
- **When in doubt, stop and wait** for an explicit go. Idle-but-correct beats busy-but-unauthorized.

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

This prompt is an **artifact for later** — something the *user* (or a fresh agent the user chooses to launch) runs when they decide to proceed. Writing it is not running it. You do **not** execute it yourself, now or as a "head start," as part of this skill.

## Phase 5 — Share, then STOP

Output the Phase 3 findings summary and the path to the plan file. **Then stop and wait — do not start executing.** Not the first step, not a "quick" setup or worktree refresh, not a single edit. The plan file is the finished deliverable and you are done.

Do **not** offer to "start now" in a way that turns the user's next reply into consent. If the user wants you to proceed, they will say so explicitly — and that go is a fresh, standalone instruction, never the answer to a question you asked. Until you hear it, hold.
