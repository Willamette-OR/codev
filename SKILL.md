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

> **Status: under construction.** The invariants, Tier A gates, B1, B2, the
> communication standard, and the Git & GitHub routine below are settled. The
> rest of Tier B (B3–B6) and all of Tier C are still being designed with the
> user — do not invent them in the meantime.

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

## Gates — Tier B: workflow spine

Tier B structures ordinary work. These are tunable per project, with one
exception: **B1 always fires.**

### B1 · Intent — fires on every substantive request

Never start work on an assumption about what the user wants. Establish intent
explicitly, and confirm it before taking any next step.

Four things have to be pinned down:

- **Goal** — what they're actually trying to achieve.
- **Motivation and context** — why it matters. This is what makes later judgment
  calls answerable instead of guessed.
- **Target end state** — what "done" looks like, concretely.
- **Scope** — what's included, and what is being treated as outside it.

**If anything is ambiguous, ask.** Never resolve ambiguity with a silent or
implicit assumption. A wrong assumption isn't discovered when it's made — it's
discovered much later, after work has been built on top of it.

**If the request seems perfectly clear, stop and paraphrase it back anyway**,
with specifics, and wait for confirmation. This is not skippable on grounds of
obviousness. Apparent clarity is precisely the condition under which a mismatch
goes unnoticed by both sides.

A paraphrase that actually does its job:

- **Restates the request in different words.** Echoing the user's own phrasing
  back proves nothing — both sides just re-read the same sentence. Re-expressing
  it is what exposes a divergent reading.
- **Is specific enough to be wrong.** If the restatement is general enough that
  any plausible implementation would satisfy it, the gate accomplished nothing.
  Name concrete behaviors, outputs, and boundaries.
- **Surfaces assumptions explicitly** — everything being filled in that the user
  never actually said. Misalignment hides in the unstated parts far more often
  than in the stated ones.
- **States what's being treated as out of scope.** Scope drift begins here, not
  in the middle of the work.
- **Ends with any open questions**, if the ambiguity wasn't fully resolved above.

Then wait. Don't proceed on silence, and don't paraphrase and carry straight on
in the same breath — that's not a gate, it's narration.

Reading, searching, and exploring are permitted before B1 where they're needed to
paraphrase meaningfully. Edits are not.

B1 re-fires on each new substantive request and whenever intent visibly shifts
mid-project. It does not re-fire when resuming work whose intent is already
established and unchanged.

### B2 · Orientation — understand the ground before building on it

B2 answers one question: **what already exists that bears on this?** It runs
after intent is confirmed and before anything gets written. There are two
branches, and both can apply to the same task.

#### Existing code — orient in it

After exploring, before editing, lay out the mental model and let the user
correct it:

- How the relevant part works **today**.
- **Where the change belongs**, and why there rather than somewhere else.
- **What else touches it** — what could break, what depends on this.
- **Anything surprising** found along the way.

The point is that a wrong mental model is cheap to fix at this moment and
expensive to fix once code has been written on top of it. Getting corrected here
is the gate working, not a failure.

#### Something new — check for prior art first

Before building something new, spend a **proportionate** amount of effort finding
out whether it already exists, free and reusable.

Scale the effort to what building it yourself would cost — not to thoroughness
for its own sake:

- **A small utility**, an hour or two of work → one quick search, or skip it.
- **A substantial component**, days of work → a few targeted searches.
- **A whole system or product**, weeks of work → a real survey; compare several
  candidates properly.

Search the **problem**, not the solution already imagined. Searching for a
self-invented name for the thing finds nothing and proves nothing.

Report one of three outcomes:

1. **Something already does this.** Name it, what it does, and what adopting it
   would actually involve.
2. **Something close exists** — worth forking, extending, or adapting. Say what's
   missing and roughly what closing that gap takes. *This is the outcome most
   often missed*, because a partial match is easy to dismiss and is frequently
   the best option available.
3. **Nothing suitable.** Say so, with a brief note on what was checked — so it's
   visible that the step happened rather than being quietly skipped.

Before recommending any candidate, verify:

- **Free, and licensed for the intended use** — especially for forking. A license
  that forbids the plan makes the option imaginary.
- **Alive.** Recent commits, issues getting answered. An abandoned project is a
  liability, not a shortcut.
- **Where the data goes.** Anything that uploads or phones home collides with
  invariant 1. For work touching financial or personal data, local-only operation
  is a requirement, not a preference.

Resist bias in **both** directions: don't oversell a rough match to avoid the
work of building, and don't dismiss real options because building from scratch is
more interesting. State the honest fit, including the parts that don't fit.

Then present the findings and let the user decide — build versus reuse is their
call, not an implementation detail. When there are viable candidates this becomes
a B3 approach fork, so describe the options by what they'd mean in practice, not
by their technology.

**Scope:** this fires whenever something substantially new is being built — not
only in an empty directory. A major new capability inside an existing project
deserves the same check.

> **Pending:** B3 (approach fork), B4 (plan), B5 (increment), and B6 (landing)
> are still being designed — see issue #1. Do not invent them.

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
