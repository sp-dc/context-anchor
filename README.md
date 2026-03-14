# 🪝 Context Anchor

**A Claude Skill that survives context compacting.**

> Built for developers using Claude Code in VSCode or the terminal, where context compacting silently drops critical project state mid-session.

---

## Before You Start: Global Install, Per-Project Initialisation

This is important to understand upfront so you know exactly what to expect.

**Installing the skill is a one-time global action.** Once installed via Claude.ai Settings, the skill is available in every Claude session you ever start — you never install it again.

**But each new project needs a one-time initialisation.** The skill can't create `CONTEXT.md` and wire up `CLAUDE.md` until it knows about your project. This only needs to happen once per project, and there are two ways it can happen:

### ✅ Best practice — initialise explicitly (recommended)

At the start of your first session on a new project, just say:

```
"Set up context anchor for this project"
```

Takes two seconds. Claude creates both files immediately, captures everything from the start, and every future session on that project is fully automatic from that point on.

### 🟡 Alternatively — let Claude detect it

If you don't initialise manually, Claude will watch the conversation and kick in automatically once it recognises the session looks like a real development project — code being written, decisions being made, a clear codebase in scope.

This works well in practice, but there's an honest caveat: **you won't always know if it's triggered yet.** If you're deep into a session and Claude hasn't created the files, you could be missing context you'd want captured. Some users will find that ambiguity uncomfortable.

That's why we recommend the explicit initialisation. One message at the start of each new project, and you never have to think about it again.

### How to check if a project is initialised

Look for `CONTEXT.md` in your project root. If it's there, you're set. If it's not, just run:

```
"Set up context anchor for this project"
```

---

## The Problem

If you've used Claude Code for a long coding session, you've hit this wall.

The context window fills up. Claude compacts silently. And suddenly:

- Architectural decisions you discussed an hour ago are gone
- Claude asks you to re-explain something you already covered
- The *why* behind important choices disappears, leaving only the *what*
- A dropped connection or dead battery means starting from scratch

There's no built-in recovery mechanism. Until now.

---

## What Context Anchor Does

Context Anchor instructs Claude to maintain a living `CONTEXT.md` file in your project folder throughout your session. It updates automatically at key moments — when decisions are made, features complete, or scope changes — so it always reflects current project state.

It also hooks into two critical moments:

**On setup:** Adds a reference to `CLAUDE.md` so every future Claude Code session automatically discovers and reads the context file — no manual loading needed.

**Before compacting:** Updates `CONTEXT.md` before the context wipe happens, so the document is at its freshest exactly when you need it most.

When compacting happens (or your battery dies, or the connection drops), paste a single paragraph from the file into a fresh session and pick up exactly where you left off:

```
You are working on Nova, a Next.js SaaS app. createCheckoutSession is complete
in src/api/stripe/checkout.ts. We're mid-way through the webhook handler —
signature verification works but customer.subscription.updated isn't written yet.
Migration not run yet. Critical: never parse the raw body before Stripe signature
verification or it always fails.
```

Fresh Claude. Fully oriented. Immediately productive.

---

## What Gets Saved

```
CONTEXT.md
├── 🚀 Resume Prompt        ← 5-sentence briefing for a fresh session
├── 📍 Current Status       ← What's being worked on right now
├── 🗺️  Project Overview    ← Stack, structure, purpose
├── 📋 Active Work          ← Current tasks with decisions and notes
├── ✅ Resolved             ← Completed work (kept for reference)
├── 📝 Decision Log         ← Why things were done the way they were
├── ⚠️  Gotchas             ← Things that will burn a fresh Claude
└── 🗄️  Archive             ← Older history, out of the way but not deleted
```

The **Decision Log** is the most important section. It's exactly what compacting destroys. "We chose Zustand over Redux because..." lives here permanently.

---

## How It Works

### Starting a session

Ask Claude to set up context for your project:

```
"Set up a context anchor for this project"
"Create a CONTEXT.md for us to track progress"
"I'm starting a new feature — save context as we go"
```

Claude creates `CONTEXT.md` in your project root and appends a reference to `CLAUDE.md`:

```markdown
# Context Anchor
See CONTEXT.md for full project context, current progress, and session resume instructions.
```

This means every future Claude Code session on this project automatically picks up the context file — even without the skill explicitly active.

### During a session

Claude updates the document automatically at natural breakpoints:

- A decision is made → added to the Decision Log immediately
- A feature completes → moved from Active to Resolved
- A gotcha is discovered → noted in Gotchas
- Scope changes → Active Work updated

You can also trigger an update manually at any time:

```
"Update the context doc"
"Log that decision about soft deletes"
"We're done with auth — update the context"
```

### Before compacting

When context runs low or you run `/compact`, Claude automatically does a **pre-compact update** first — flushing any unrecorded decisions, capturing in-progress state, and rewriting the Resume Prompt to reflect exactly where things stand. Then it confirms:

```
✅ CONTEXT.md updated — safe to compact
```

### After a compact / fresh session

Claude Code reads `CLAUDE.md` automatically on startup, sees the reference, and loads `CONTEXT.md`. No manual steps needed. For extra speed, copy the **Resume Prompt** section as your first message in the new session.

---

## Example: What CONTEXT.md Looks Like

<details>
<summary>Click to see a real example</summary>

```markdown
# Nova App — Context Anchor
> Last updated: 2026-03-14 11:28 | Session: PRE-COMPACT UPDATE — Stripe checkout done, webhook 80% complete

---

## 🚀 Resume Prompt

You are working on **Nova**, a Next.js 14 SaaS app with Stripe billing. The
`createCheckoutSession` function is complete in `src/api/stripe/checkout.ts`.
We are mid-way through the webhook handler — signature verification works but
`customer.subscription.updated` is not yet written. Prisma `Subscription` model
exists but migration has NOT been run yet. **Critical:** never parse the raw
request body before Stripe signature verification or it always fails.

---

## 📝 Decision Log

| Decision | Chosen approach | Reason | Date |
|----------|----------------|--------|------|
| Billing UI | Stripe Hosted Checkout | Avoids PCI compliance complexity | 2026-03-14 |
| Webhook idempotency | Store `stripeEventId` | Stripe can send duplicate events | 2026-03-14 |

---

## ⚠️ Gotchas & Known Issues

- Raw body required for Stripe webhook verification — never parse before `constructEvent()`
- Raw body middleware must come BEFORE JSON parser in Next.js middleware chain
- Prisma migration not yet run — `Subscription` table does not exist in DB yet
```

</details>

---

## Installation

### Option 1: Install via Claude.ai Skills (recommended)

1. Download [`context-anchor.skill`](./context-anchor.skill)
2. In Claude.ai, go to **Settings → Skills**
3. Click **Install Skill** and select the downloaded file

### Option 2: Manual install (Claude Code / VSCode)

Copy `SKILL.md` into your Claude skills directory:

```bash
cp SKILL.md ~/.claude/skills/context-anchor/SKILL.md
```

---

## Git: Should I commit these files?

**`CONTEXT.md`** — your call:
- Commit it to resume on a different machine or share context with teammates
- Gitignore it if it contains session-specific notes you'd rather keep local

**`CLAUDE.md`** — worth committing. It's a standard Claude Code config file and helps any machine or teammate onboard to your project preferences instantly.

---

## The Archive System

Context Anchor uses a three-tier structure to keep the document lean without losing history:

| Tier | Contents | When items move here |
|------|----------|---------------------|
| **Active** | Current tasks, open questions | — |
| **Resolved** | Completed work | When a task is done |
| **Archive** | Older history | When Resolved exceeds ~5 items |

Nothing is ever permanently deleted. It just moves down the tiers.

---

## Contributing

Issues and PRs welcome. If you've found a pattern that works well — a section structure, a prompt format, a workflow trigger — open an issue and let's add it.

---

## License

MIT — free to use, fork, and adapt.

---

*Built as a free community skill. If it saves your session, share it with someone who's hit the same wall.*
