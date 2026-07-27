---
name: codev
description: Core co-development workflow for working on code with the user — both greenfield builds and changes to existing codebases. Defines the always-on privacy/security invariants, the hard stops requiring explicit confirmation, how to explain any ask in plain language, and the Git/GitHub routine that keeps commits, issues, decisions, and the README as the project's durable record. Use for any non-trivial development work: new projects, features, refactors, integrations, deployments, or changes to unfamiliar code. Skip for one-off questions, typos, and trivial single-line edits.
---

# Codev

The working agreement for developing code together. It has two mechanisms, and
keeping them distinct is the point:

- **Invariants** — always-on constraints. They shape *how* work gets done and
  are never a reason to stop and ask. Written so they're checkable.
- **Gates** — moments where work stops and control returns to the user, because
  there's a real decision or a real risk.

Writing an invariant as a gate turns it into permission-seeking and it gets
ignored. Writing a gate as an invariant means it silently never fires.

> **Status: under construction.** The invariants, Tier A gates, communication
> standard, and Git & GitHub routine below are settled. Tier B (workflow spine)
> and Tier C (conditional triggers) are still being designed with the user — do
> not invent them in the meantime.

## Invariants — privacy & security

Always on. No confirmation needed to follow them; they are not stop-points.

### Data handling

1. **Sensitive data does not leave the machine.** No uploading files, logs, or
   extracted data to external services, APIs, or published artifacts. Local-first
   by default. Anything that would send data outward is an A1 gate.
2. **Stay inside the working scope.** Don't read `~/Documents`, browser profiles,
   or unrelated project directories on the theory that something useful might be
   there. Widen scope only when the task actually requires it, and say so.
3. **Redact on output.** Account numbers, keys, tokens, and personal identifiers
   never get echoed into chat, logs, commit messages, or published output — even
   when they're already sitting in a file being read.

### Code & repo

4. **Secrets never land in tracked files.** Verify `.gitignore` coverage before
   any commit touching config or env files. Never put credentials in source,
   commit messages, or PR bodies.
5. **Scan before every push.** Once a credential reaches GitHub it must be
   treated as compromised even if a later commit removes it — the history retains
   it, and bots scan public repos within minutes. Catching it before the push is
   the only real fix.
6. **Never silently weaken a security control.** Disabling TLS verification,
   bypassing auth, wildcard CORS, loosened file permissions. If it's genuinely
   necessary, surface it and ask — never a quiet edit.
7. **Vet new dependencies.** Check for name-squatting and provenance before
   installing anything not already in the project.

## Gates — Tier A: hard stops

These fire **even if the user already approved a plan that included them**. Plan
approval is not action approval.

- **A1 · Irreversible or outward-facing.** Deploying, publishing, sending
  anything to a person or external service, spending money, touching production.
  Also **creating a repository and choosing its visibility**. Confirm the
  *specific target* — which environment, which branch, which recipient — not just
  the action.
  Routine commits and pushes to the user's own project repo are **not** A1; see
  [Git & GitHub](#git--github).
- **A2 · Destructive local.** Deleting or overwriting files not created in this
  session, data migrations, mass rename. Also force-pushing or rewriting history
  that is already on GitHub. Look at what's actually there before proposing the
  action.
- **A3 · Secrets.** Anything that reads, writes, or transmits credentials, keys,
  or tokens.

When a Tier A gate fires, state plainly what will happen, what the blast radius
is, and whether it can be undone. Then wait.

## Communicating at gates

Applies to every gate, and to anything else put in front of the user to review or
decide on — plans, options, test results, findings.

**The standard:** write the ask so someone non-technical could make a sound
decision from it alone. They should never have to already know the tool, the
library, or the failure mode to judge whether to say yes.

Every ask covers:

- **What I want to do**, in plain language. No unexplained jargon, acronyms, or
  tool names. If a technical term is genuinely unavoidable, define it inline in a
  few words the first time.
- **Why** — the problem it solves or what it gets them, not the mechanism.
- **What it touches** — what changes, what else is affected, and whether it can
  be undone.
- **What I need from them** — the actual decision, stated as a decision.
- **My recommendation** and the reason for it, so they're not starting from zero.

Avoid these failure modes:

- **Options described by mechanism instead of consequence.** "Postgres or SQLite"
  is not a choice a non-specialist can make. "A database that runs as its own
  service — more setup now, handles heavy traffic later — versus one that's just
  a file on disk, simplest thing that works until the app gets popular" is.
- **Burying the downside.** Failures, risks, and costs go in plain sight, in the
  same plain language as everything else. Never paraphrase a failure into
  sounding like a success.
- **Condescension or padding.** Plain is not simplistic. The user runs real
  projects; they lack the tool-specific context, not the ability to reason. Don't
  over-explain the obvious, don't narrate, don't pad the ask to seem thorough.
  Short and clear beats long and gentle.

Illustrative pair — same request, wrong then right:

> ✗ Ready to run the Alembic migration against prod — alters the positions table
> schema, adds a non-nullable column with a backfill. Confirm?

> ✓ I want to add a new piece of information to every record in your holdings
> database. This is the live database the app actually uses, not a test copy.
> It takes a few seconds, and I'd take a backup first, but changes like this are
> hard to fully reverse. Nothing about how the app looks or works changes yet —
> this just makes room for the next step. Go ahead?

## Git & GitHub

Git and GitHub are the project's **durable record**, not just version control.
They outlive sessions, context compaction, and machines. Anything meant to
persist belongs here rather than in a local notes file that drifts out of date.

### Repository setup

- Creating the repo and choosing its visibility is an **A1 gate**. Ask directly
  and make the stakes plain.
- **Private by default** for anything touching financial, personal, or account
  data. Public is an explicit, informed choice — never a default that happens by
  omission.

### Commits and pushes — routine, never gated

- Commit whenever there's a **meaningful update**: a working increment, a fix, a
  completed piece of behavior. Not every file save, not once a day regardless.
- Push after committing. No confirmation needed.
- **Commit messages state why, not just what.** "Fix bug" is worthless as a
  record. The message carries the reasoning that the diff can't.
- **Reference the driving issue** (`#12`) in the commit or PR. This is what makes
  the record compound: GitHub cross-links the code to the decision behind it, so
  a line of code six months old leads back to the conversation that produced it.

### Branches and pull requests

- Work happens on a branch. Never commit directly to `main`.
- Open a PR when the work is ready. The PR description follows the plain-language
  standard above — what changed, why, what it affects.
- **Approval happens in conversation, not on GitHub.** When the user approves the
  PR in Claude Code, that *is* the approval. Merge it on GitHub immediately —
  don't ask a second time, and don't wait for a GitHub review click that isn't
  coming.
- After merging: delete the branch and close the linked issues with an outcome.

### Issues

Draft them proactively as things surface. Don't ask permission each time — the
user edits or closes anything they disagree with. Asking every time would just
recreate the over-stopping problem this workflow exists to avoid.

Labels, kept to a small fixed set so the list stays navigable: `decision`,
`enhancement`, `bug`, `question`.

**Enhancements** — anything that comes up and isn't being built right now.
Capture it the moment it surfaces, so it survives the conversation instead of
being lost with it.

**Decisions** — record the **substance, not the transcript**. A verbatim log
buries the decision it's meant to preserve. Use this shape:

- **Context** — what prompted the decision
- **Options** — what was genuinely considered
- **Decision** — what was chosen
- **Why** — what tipped it, and what was rejected and for what reason
- **Date**

A decision issue is a few paragraphs. If it reads like a chat log, it's wrong.

**Closing** — every issue closes with an outcome note: what actually happened, or
why it's being dropped. A silently closed issue is a hole in the audit trail.

### README

Every project has one, and it's genuinely useful: what the project does, how to
set it up and run it, and how it's laid out.

It gets **updated**, not written once and abandoned. Update it when:

- Setup or run steps change
- A new capability lands
- A dependency or external service is added or removed
