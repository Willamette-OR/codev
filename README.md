# codev

A Claude Code skill defining a working agreement for developing software
together — covering both greenfield builds and changes to existing codebases.

> **Status: under construction.** The invariants, Tier A gates, communication
> standard, and Git & GitHub routine are settled. Tier B (workflow spine) and
> Tier C (conditional triggers) are still being designed.

## What it does

`codev` governs *how* Claude works on a codebase rather than what it builds. It
exists to solve two opposite failure modes at once: an assistant that charges
through irreversible actions without asking, and one that stops so often the
workflow dies of friction.

It is built on one organizing distinction:

- **Invariants** — always-on constraints that shape how work gets done, and are
  never a reason to stop and ask.
- **Gates** — the specific moments where work halts and control returns to the
  user, because there is a real decision or a real risk.

Collapsing these into one mechanism is what makes process documents fail. An
invariant written as a gate becomes permission-seeking and gets ignored; a gate
written as an invariant silently never fires.

Two further principles run through it:

- **Gate on risk, not on progress.** Stops happen where something is
  irreversible or genuinely undecided — not merely because a unit of work ended.
- **Any ask must be decidable by a non-specialist.** Every request for approval
  explains what will happen, why, what it affects, whether it can be undone, and
  what decision is needed — in plain language, with a recommendation.

## Contents

| Section | Purpose |
| --- | --- |
| Invariants | Privacy and security constraints that are always in force |
| Tier A gates | Hard stops — irreversible, destructive, or secret-touching actions |
| Communicating at gates | The plain-language standard for every ask |
| Git & GitHub | Commits, branches, PRs, issues, and the README as durable project record |

## Installation

Clone into the Claude Code skills directory:

```bash
git clone https://github.com/Willamette-OR/codev.git ~/.claude/skills/codev
```

Claude Code discovers it automatically on the next session. It loads when
non-trivial development work begins, and stays out of the way for one-off
questions and trivial edits.

## Layout

```
codev/
├── SKILL.md    # the skill itself — instructions loaded into Claude's context
└── README.md   # this file
```

## Design notes

`codev` replaces an earlier skill, `phased-build`, which gated at every
milestone boundary. That proved too heavy: many milestones ended with nothing
actually worth deciding, so the checkpoints became ceremony. It also handled
only greenfield work, with no provision for the far more common case of changing
code that already exists.
