# Personal instructions

## Handoff file (`handoff_<ticket>.md` at repo root)

The file is named after the Jira ticket: key lowercased, hyphen → underscore (`SQ-5002` → `handoff_sq_5002.md`). Take the key from my message or the branch name (`kturk/sq-5002-3-...` → `SQ-5002`). Without a ticket, use `handoff.md`.

**Before starting any work:** check whether the handoff file for the current ticket exists at the repo root (also glance at other `handoff_*.md` files if the ticket is unclear).
- If it exists, read it first and treat it as the context for the current work.
- If its **Branch** doesn't match the current branch (`git rev-parse --abbrev-ref HEAD`), it belongs to other work. Tell me, and don't act on it until I confirm.

**If there's no handoff file for the ticket and the work comes from a Jira ticket** (a ticket key like `SQ-1234` in my message or the branch name), create one before writing any code. Use the template and steps in `~/.claude/skills/handoff/SKILL.md`. Fill it from the ticket, the branch and the conversation so far.

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

## Coding guidelines

These favour caution over speed. Use judgment on trivial tasks.

### 0. Follow Bitly's standards

Always follow Bitly's coding standards in the `bitly/docs` repo, under `standards/`. Read the local clone at `/bitly/src/github.com/bitly/docs/standards/`. If it is missing, fetch the files with `gh api repos/bitly/docs/contents/standards/<file>`.
- Start at `standards/README.md`. It lists every standard and its scope.
- Before you write code, read the standards that cover it: always `coding_style.md` and `general_naming_guidelines.md`; for Go, the `go_*.md` files that apply (errors, logging, testing, imports, `context`, settings); for Python, `python_style.md`; for TypeScript, `frontend_standards.md`. Read others (MySQL, NSQ events, public APIs, secrets) when the work touches them.
- Bitly's standards always take priority. If one conflicts with the rules below, follow the standard and tell me about the conflict.

### 1. Think before coding

Don't assume, and don't hide confusion. Before you write code:
- State your assumptions. If unsure, ask.
- If the request has more than one reading, set them out. Don't pick one silently.
- If a simpler approach exists, say so. Push back when you should.
- If something is unclear, stop, name what confuses you, and ask.

### 2. Simplicity first

Write the least code that solves the problem.
- No features I didn't ask for.
- No abstractions for code used once.
- No options or settings I didn't ask for.
- No error handling for cases that can't happen.
- If 200 lines could be 50, rewrite it.

Ask: would a senior engineer call this too complex? If so, simplify.

### 3. Change only what you must

Clean up only your own mess.
- Don't "improve" nearby code, comments or formatting.
- Don't refactor what isn't broken.
- Match the existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it. Don't delete it.
- Remove imports, variables and functions that your change left unused. Leave older dead code alone unless I ask.

Every changed line should trace back to my request.

### 4. Work toward goals you can check

Turn each task into a goal you can verify:
- "Add validation" → write tests for bad input, then make them pass.
- "Fix the bug" → write a test that reproduces it, then make it pass.
- "Refactor X" → make sure tests pass before and after.

For tasks with several steps, state a short plan, each step with its check:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
```

When a handoff file exists, the plan goes in its **Implementation steps**. Keep working on a step until its check passes, then stop for review as "Working through steps" says. Don't run on into the next step.

## Writing style

Follow George Orwell's six rules (from *Politics and the English Language*) in everything you write: replies to me, handoff files, code comments, commit messages, PR descriptions and review comments.

1. Never use a metaphor, simile or other figure of speech that you are used to seeing in print.
2. Never use a long word where a short one will do.
3. If it is possible to cut a word out, always cut it out.
4. Never use the passive where you can use the active.
5. Never use a foreign phrase, a scientific word or a jargon word if you can think of an everyday English equivalent. Exact technical names (function names, Go terms, product terms like "SSO") are fine. They are the plain word here.
6. Break any of these rules sooner than say anything outright barbarous.
