# Coordinator Rules

**Read this only if you are a coordinator:** a session that starts, tracks or manages other sessions (a pinned
"task board" session, a project's coordinator chat). A thread doing the work itself skips this file.

Why it exists: a few long-lived threads sat at 520K–630K tokens of context. Every step re-reads the whole
context, so cache reads were most of the usage and those threads burned the 5-hour limit. A thread's knowledge
belongs in docs, not in its context.

## 1. Watch context size

- On every status refresh, read each running thread's context size (desktop app: `get_usage(session_id)`; an
  idle session reports "unavailable", so read the last `usage` entry of its transcript: input + cache read +
  cache creation tokens).
- Write the size next to each pending row on the task board.

| Context | Action |
|---|---|
| under 300K | nothing |
| 300K–400K | tell the thread to wrap at its next seam (a step verified, a question settled) |
| over 400K | tell the thread to finish its current step and wrap now |
| task done (any size) | wrap now; nothing new goes into a finished thread |

A follow-up on a finished task goes to a fresh thread seeded from the hand-off, not back into the old one.

## 2. Wrap, then hand off

Ask the thread itself to wrap; only it knows what is in its context. Never wrap on its behalf.

- **With the `wrap` skill:** send `/wrap standby next: <task>`. It writes decisions, the code map, doc updates and
  memory, then hands off to an idle session titled `Standby`, renames and pins it, and unpins itself. With no
  next task, plain `/wrap`; the fresh thread starts later with `/resume-feature <feature>`.
- **No Standby session:** start one fresh, brief = `/resume-feature <feature>` + `Next task: <task>`.
- **Projects without the wrap skill:** ask the thread to write its state into the project's `HANDOFF.md`
  (decisions and why, state of the tree, single next step) and its `AGENTS.md` where a fact is durable, and
  reply with the hand-off. Then start the fresh thread with "Read `HANDOFF.md` first. Next task: <task>."
- Wait for the thread's wrap report before starting the fresh one, so the new thread reads finished docs.
- Name the new thread `<Feature>: <short task>` at start; never leave it as `Standby` or "Local session…".

## 3. Retire the old thread

After the fresh thread has confirmed it picked up the task:

- Move its board row to Done with a pointer to the new thread.
- Do not send it further work.
- **Desktop sessions:** never archive them. Unpin and mark completed is the whole retirement.
- **Claude Projects threads:** resolve the old thread once the new thread is running, so it cannot restart and
  pick up work again. It stays in the project's history.

## 4. Keep the coordinator small too

The coordinator is a long-lived thread and the rules above apply to it. Read thread state in small slices (latest
events only), keep conclusions on the board rather than in context, and when the coordinator itself passes 300K,
update the board and hand off to a fresh coordinator that starts by reading this file and the board.
