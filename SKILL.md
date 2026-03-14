---
name: context-anchor
description: >
  Maintains a persistent, living project context document (CONTEXT.md) in the user's project folder
  that survives context compacting, connection drops, and session resets. Use this skill whenever:
  starting a new coding project or feature, the user mentions running out of context, working in
  VSCode or Claude Code on multi-session work, beginning a complex multi-step task, or when the user
  asks to "set up context", "create a project doc", "save progress", or "pick up where we left off".
  Also trigger proactively during long sessions when significant decisions or milestones have occurred.
  This skill is the safety net for everything that compacting silently destroys.
---

# Context Anchor

A skill for creating and maintaining a living `CONTEXT.md` file that keeps project state, decisions,
and progress intact across context compacts, session resets, and connection drops.

## Core Philosophy

The document is a **co-pilot log**, not just notes. It's written so that a brand-new Claude instance
with zero memory can read it and immediately be productive. Every update should pass this test:
*"Could a fresh Claude pick up the project from this file alone?"*

---

## When to Use This Skill

- **Session start**: User begins a new project, feature, or complex task
- **Proactively during a session**: After significant decisions, architectural choices, or completed milestones
- **On request**: User says anything like "save context", "update the doc", "log this", "note that down"
- **Before risky operations**: Large refactors, dependency changes, structural changes
- **After compacting warnings**: If the user mentions context is getting low

---

## Document Structure

Always create/maintain the file at `CONTEXT.md` in the project root (or `docs/CONTEXT.md` if a `docs/` folder exists).

```markdown
# [Project Name] — Context Anchor
> Last updated: [timestamp] | Session: [brief session descriptor]

---

## 🚀 Resume Prompt
<!-- CRITICAL: Keep this section ≤5 sentences. A human or Claude should be able to paste this
     into a fresh session and be immediately oriented. Update this every session. -->

[Single paragraph that captures: what the project is, where we are right now, what the immediate
next step is, and any critical constraint or gotcha to be aware of.]

---

## 📍 Current Status
**Working on:** [specific current task]  
**Blocked by:** [anything blocking progress, or "Nothing"]  
**Next up:** [the very next concrete action]

---

## 🗺️ Project Overview
<!-- Stable section — only update when the project fundamentally changes -->

**Purpose:** [What this project does and why]  
**Stack:** [Key technologies, languages, frameworks]  
**Structure:** [Key files/folders and what they do — keep this as a brief map, not exhaustive]  
**Repo/Location:** [path or URL if relevant]

---

## 📋 Active Work
<!-- Items currently in progress. Move to Resolved when done. -->

### [Task/Feature Name]
- **Goal:** [what we're trying to achieve]
- **Approach:** [how we're doing it]
- **Status:** [in progress / blocked / review]
- **Key decisions made:**
  - [decision] — *because [reason]*
- **Notes:** [anything a fresh Claude needs to know about this task]

---

## ✅ Resolved
<!-- Completed work. Keep the last 3–5 items. Archive older items to ## 🗄️ Archive -->

### [Completed Task] ✓
- **What was done:** [brief summary]
- **Key decision:** [most important choice made and why]
- **Outcome:** [result / files changed / behaviour introduced]

---

## 📝 Decision Log
<!-- Why we made important choices. This is what compacting destroys most. -->

| Decision | Chosen approach | Reason | Date |
|----------|----------------|--------|------|
| [e.g. State management] | [e.g. Zustand over Redux] | [e.g. Simpler API, less boilerplate for this scale] | [date] |

---

## ⚠️ Gotchas & Known Issues
<!-- Things that burned us, or will burn a fresh Claude who doesn't know the codebase -->

- [e.g. `auth.ts` uses a custom session format — don't use the standard next-auth types directly]
- [e.g. The dev server must be started with `--legacy-peer-deps` or it crashes]

---

## 🗄️ Archive
<!-- Old completed work and superseded decisions. Kept for reference, not active reading. -->
<!-- Move items here from Resolved when the list exceeds ~5 items -->

<details>
<summary>Archived items (click to expand)</summary>

[Older completed tasks and decisions go here]

</details>
```

---

## Operational Rules

### Creating the document (first time)
1. Ask the user for the project root path if not obvious from context
2. Check if a `CONTEXT.md` already exists — if so, read and update it rather than overwrite
3. Fill in every section based on the current conversation
4. Write the Resume Prompt last — it should reflect the completed document
5. Tell the user the file has been created and briefly explain the triage pattern

### Updating the document (ongoing)
Claude should suggest or offer updates at these natural breakpoints:
- A feature or task is completed → move to Resolved, update Status
- An architectural decision is made → add to Decision Log immediately
- A gotcha is discovered → add to Gotchas & Known Issues
- A task is added or scope changes → update Active Work
- Resolved section exceeds 5 items → move oldest to Archive
- End of a productive session → update Resume Prompt

### The triage pass
At natural breakpoints (end of a feature, before a big change, when asked), Claude should:
1. Review Active Work — is anything done that hasn't been moved?
2. Review Resolved — does anything need to go to Archive?
3. Review Decision Log — are there undocumented decisions from this session?
4. Update the Resume Prompt to reflect current state

### Resume Prompt guidelines
This is the most important section. Rules:
- Maximum 5 sentences
- Must answer: What is this? Where are we? What's next? Any critical gotcha?
- Written in second person ("You are working on...")
- Should be pasteable directly into a new Claude session as an opening message

---

## Tone & Style

- Write as if briefing a capable colleague who has never seen the project
- Be specific — "uses Prisma ORM with PostgreSQL" beats "uses a database"
- Record the *why* not just the *what* — decisions without reasons are half-useless
- Keep Active Work honest — don't leave completed tasks there
- Don't pad — a tight, accurate document beats a comprehensive but stale one

---

## Example Resume Prompt

> You are working on **Velox**, a React + Fastify task management app. We just finished the authentication flow using JWT with refresh tokens stored in httpOnly cookies — the implementation is in `src/auth/`. The immediate next step is building the task creation API endpoint (`POST /tasks`) with Zod validation. Watch out: the Fastify instance uses a custom plugin order in `server.ts` that must be preserved or the auth middleware breaks.

---

## File Location Priority

1. `CONTEXT.md` in project root (default)
2. `docs/CONTEXT.md` if a `docs/` folder exists
3. `.claude/CONTEXT.md` if the user prefers hidden/tool config folders
4. Wherever the user specifies

---

## CLAUDE.md Integration

Claude Code automatically reads `CLAUDE.md` at the start of every session before the user types anything. Context Anchor hooks into this so the context file is discovered automatically — even without the skill explicitly active.

### When creating CONTEXT.md for the first time:
1. Check if `CLAUDE.md` exists in the project root
2. **If it exists:** Append the following line to the bottom (never overwrite anything):
   ```
   See CONTEXT.md for full project context, current progress, and session resume instructions.
   ```
3. **If it doesn't exist:** Create `CLAUDE.md` with just that single line
4. Tell the user: "I've added a reference in CLAUDE.md so every future Claude Code session automatically picks up the context file"

### Rules:
- Only add the line once — check the file first, skip if the reference already exists
- Never overwrite or restructure an existing `CLAUDE.md` — append only
- If the user has a complex `CLAUDE.md`, add the line under a `# Context Anchor` comment

---

## /compact Hook — Pre-Compact Update

When Claude Code runs out of context it compacts — summarising the session and restarting fresh. This hook ensures `CONTEXT.md` is always updated *before* that happens, so the document is at its freshest exactly when it's needed most.

### When to trigger a pre-compact update:
- The user runs `/compact` manually
- Claude detects context is running very low
- The user says anything like "we're running out of context", "context is getting full", or "about to compact"

### Pre-compact update steps (in order):
1. **Update Current Status** — what is being worked on right this moment
2. **Flush the Decision Log** — any decisions made since last update that aren't yet recorded
3. **Update Active Work** — capture in-progress state, half-finished work, open questions
4. **Update the Resume Prompt** — most critical step; must reflect exactly where things stand right now
5. **Add any new Gotchas** discovered this session
6. Confirm to the user: "✅ CONTEXT.md updated — safe to compact"

### Why this order matters:
The Resume Prompt must be written *after* capturing everything else, so it accurately reflects the full current state. A pre-compact update written in the wrong order risks the Resume Prompt being stale by the time it's needed.

---

## Git Considerations

If the project uses Git, mention to the user that they may want to:
- Add `CONTEXT.md` to the repo — useful for team handoffs and resuming on other machines
- Or add it to `.gitignore` if it contains session-specific notes they don't want committed

Note: `CLAUDE.md` is generally worth committing — it's a standard Claude Code config file and helps any machine or teammate onboard instantly.

Don't make these choices for them — just surface it once when creating the files.
