# Personal instructions

## Handoff file (`handoff_<ticket>.md` at repo root)

The file is named after the Jira ticket: key lowercased, hyphen → underscore (`SQ-5002` → `handoff_sq_5002.md`). Take the key from my message or the branch name (`kturk/sq-5002-3-...` → `SQ-5002`). Without a ticket, use `handoff.md`.

**Before starting any work:** check whether the handoff file for the current ticket exists at the repo root (also glance at other `handoff_*.md` files if the ticket is unclear).
- If it exists, read it first and treat it as the context for the current work.
- If its **Branch** doesn't match the current branch (`git rev-parse --abbrev-ref HEAD`), it belongs to other work. Tell me, and don't act on it until I confirm.

**If there's no handoff file for the ticket and the work comes from a Jira ticket** (a ticket key like `SQ-1234` in my message or the branch name), create one before writing any code. Use the template and steps in `.claude/skills/handoff/SKILL.md`. Fill it from the ticket, the branch and the conversation so far.

**Keep it current as the conversation goes on.** Update the handoff file when any of these happen, without being asked:
- a decision is made (or an approach is rejected), with the reason
- a task is finished, or a new one is found
- a gotcha, blocker or open question comes up, or is resolved
- review feedback arrives or is addressed
- the next steps change

Keep edits small and remove anything stale. Never commit any `handoff*.md` file.

## Working through steps

Work through the **Implementation steps** and **Review fixes** in the handoff file one at a time. When you finish a step:
1. If the step changed more than 250 lines of code, ask me whether to run `/simplify` on the diff, and run it only if I say yes. For smaller steps, don't run it or ask.
2. Run the tests that cover the code you touched, and fix any failures.
3. Update the handoff file: note the test results and mark the step in progress for review.
4. Stop. Tell me what changed, what you simplified (if anything) and how the tests went, then wait for my review.

Don't start the next step until I've reviewed this one. Tick the step `[x]` once I approve it.

## Writing style

Follow George Orwell's six rules (from *Politics and the English Language*) in everything you write: replies to me, handoff files, code comments, commit messages, PR descriptions and review comments.

1. Never use a metaphor, simile or other figure of speech that you are used to seeing in print.
2. Never use a long word where a short one will do.
3. If it is possible to cut a word out, always cut it out.
4. Never use the passive where you can use the active.
5. Never use a foreign phrase, a scientific word or a jargon word if you can think of an everyday English equivalent. Exact technical names (function names, Go terms, product terms like "SSO") are fine. They are the plain word here.
6. Break any of these rules sooner than say anything outright barbarous.
