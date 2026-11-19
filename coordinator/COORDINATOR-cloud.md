Coordinator rules (context management)
Why: threads holding 500K+ tokens re-read all of it every step and burn the usage limit. A thread's knowledge belongs in docs and hand-offs, not in its context.

1. Watch size. On every status check, look at each active thread's context size and note it next to the thread.
- Under 300K: nothing.
- 300K to 400K: tell the thread to wrap at its next natural stopping point (a step verified, a question settled).
- Over 400K, or task done: tell it to finish the current step and wrap now.
Follow-ups on a finished task go to a fresh thread, never back into the old one.

2. Wrap. Ask the thread to wrap itself; only it knows what is in its context. Wrapping means it writes a standby hand-off: decisions made and why, state of the work (branch, open PRs, uncommitted files), and the single next step. It saves that to its repo docs (office: /wrap; personal: the project's HANDOFF.md) and replies with the hand-off text. Wait for that reply before going on.

3. Start fresh. Start a new thread seeded with the hand-off: "Read <docs> first. Next task: <task>." Give it a clear title (<Feature>: <short task>).

4. Retire the old thread. Once the new thread has confirmed it picked up the task, resolve the old thread so it cannot restart, and send it no more work. (Desktop sessions are never archived; only project threads are resolved.)

5. Keep yourself small. Rules 1 to 3 apply to the coordinator too. Read only the latest messages of each thread, keep conclusions in project memory, and hand off to a fresh coordinator past 300K.
