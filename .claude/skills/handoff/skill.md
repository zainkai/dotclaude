---
name: handoff
description: Write a handoff_<ticket>.md (e.g. handoff_sq_5002.md) capturing the current branch/PR context so a fresh Claude Code agent can pick up the work cold.
argument-hint: [optional notes or focus for the next agent]
disable-model-invocation: true
allowed-tools: Bash(git status:*), Bash(git log:*), Bash(git diff:*), Bash(git merge-base:*), Bash(git rev-parse:*), Bash(git branch:*), Bash(gh pr view:*), Read, Write
---
# Handoff

Write `handoff_<ticket>.md` at the repo root (see "File name" below): a self-contained briefing for a new Claude Code agent that has **zero** context on this branch. The reader can run git and read code. Don't restate what it can trivially derive (the full diff). Do capture what lives only in this conversation or in your head: intent, decisions, dead ends, gotchas, and what's left.

Extra notes from the user (fold these in and give them priority): $ARGUMENTS

## Snapshot (gathered at invocation)

Branch: !`git rev-parse --abbrev-ref HEAD`

Commits on this branch vs main:
!`git log --oneline "$(git merge-base HEAD origin/main 2>/dev/null || git merge-base HEAD main)"..HEAD`

Files changed vs main:
!`git diff --stat "$(git merge-base HEAD origin/main 2>/dev/null || git merge-base HEAD main)"`

Working tree:
!`git status --short`

PR (if one exists):
!`gh pr view --json number,title,url,state,isDraft,reviewDecision,body 2>/dev/null || echo "no PR found for this branch"`

## File name

Name the file after the Jira ticket: `handoff_<ticket>.md`, with the key lowercased and the hyphen turned into an underscore. `SQ-5002` → `handoff_sq_5002.md`.
- Take the key from the user's notes or the conversation first, then from the branch name (`kturk/sq-5002-3-...` → `SQ-5002`). Use the project key and number only; drop part suffixes like `-3`.
- If there's no ticket at all, fall back to `handoff.md`.

## Steps

1. Use the snapshot above. Read the changed files and any untracked plan/notes docs at the repo root (e.g. `*_plan.md`, `*_qa.md`) that relate to this branch. Pull the Jira ticket (key is usually in the branch name, e.g. `sq-5009` → `SQ-5009`) if the Atlassian tools are available.
2. Mine the current conversation for anything not in git: decisions and why, approaches tried and rejected, open review comments, user preferences given for this work, blockers, and the next concrete step.
3. If `handoff_<ticket>.md` already exists, read it first and update it rather than blindly overwriting. Drop anything stale. If only a legacy `handoff.md` exists for this same branch, use it as the starting point and write the new file under the ticket name (tell the user so they can delete the old one).
4. Break the ticket into implementation steps, and each PR comment you'll fix into review fixes (see below). If the file already has steps, keep them, tick off finished ones, and add or split steps as new work turns up.
5. Write `handoff_<ticket>.md` using the template below. Use file paths with line numbers (`path/to/file.go:123`) so the next agent can jump straight there. Keep it tight. Aim for something readable in two minutes. Omit empty sections.
6. Don't commit it. Tell the user where it is and name the next unchecked step.

## Implementation steps

Split every ticket into small code steps the user can review one at a time. List only the ticket's own code work (code, tests, config, templates). Code changes asked for in PR comments go under "Review fixes". PR replies, deploys, manual QA and other chores go under "Next steps". Each step should:
- do one thing, and leave the code building and tests passing
- touch a small set of files, so the diff takes a few minutes to read
- say what "done" means: the tests to run or the behavior to check

Order the steps so each builds on the last. Prefer more, smaller steps over a few big ones. Mark a step `[x]` only after the user has reviewed it.

## Review fixes

List the code changes asked for in PR comments, grouped by comment, under the same rules as implementation steps. Name the reviewer and link or cite the comment. Add a comment's fixes only after the user agrees to act on it.

## Template

```markdown
# Handoff: <branch> — <one-line purpose>

- **Branch:** `<branch>` (base `main`)
- **PR:** <url / number, state, review status> | none yet
- **Ticket:** <JIRA key + title> (parent / blockers if relevant)
- **Updated:** <YYYY-MM-DD>

## Goal
What this branch is meant to do and why, in 2–5 lines.

## Implementation steps
- [x] 1. <step> — <files touched>. Check: <test command or behavior>
- [ ] 2. <step> — <files touched>. Check: <…>  ← in progress
- [ ] 3. <step> — …

## Review fixes
### <reviewer> — <comment summary> (`file.go:123`)
- [x] 1. <fix> — <files touched>. Check: <…>
- [ ] 2. <fix> — …

Uncommitted changes: <summary or "none">

## Key changes
Brief map of the commits/files that matter and what each does. Don't paste the diff.

## Decisions & rationale
Non-obvious choices and why. Include what was tried and rejected so the next agent doesn't retry it.

## Gotchas
Traps, surprising behavior, environment quirks, related tickets that change the plan.

## Open questions / review feedback
PR comments not yet decided on, things waiting on the user or another team.

## Next steps
Ordered actions: the next unchecked step, plus work that isn't code (PR replies, deploys, manual QA, re-review requests).

## How to verify
Commands to build/test/QA this change (e.g. `go test ./sms_messaging/...`), plus any manual QA docs.

## Related docs
Plan/QA files, Jira links, Confluence pages.
```
