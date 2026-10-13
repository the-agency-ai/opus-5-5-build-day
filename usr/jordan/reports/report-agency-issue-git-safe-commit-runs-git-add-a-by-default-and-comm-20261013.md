---
report_type: agency-issue
issue_type: bug
filing_agent: jordan
filed_by: jordan
date_filed: 2026-10-13
target_repo: the-agency-ai/the-agency
github_issue: https://github.com/the-agency-ai/the-agency/issues/493
github_issue_number: 493
status: open
---

# git-safe-commit runs 'git add -A' by default and commits the whole working tree; --staged is undocumented

**Filed:** 2026-10-13T07:34:36Z
**Target:** [the-agency-ai/the-agency](https://github.com/the-agency-ai/the-agency)
**Issue:** [#493](https://github.com/the-agency-ai/the-agency/issues/493)
**Type:** bug
**Status:** open

## Filed Body

**Type:** bug

## Problem

`agency/tools/git-safe-commit` runs `git add -A` by default, so it stages and commits the **entire working tree**, not just the files the agent staged. The `--staged` flag that turns this off isn't shown in `--help`, and the `/git-safe-commit` skill doesn't mention it either.

An agent following the skill as written does this:
1. Stages a few files carefully with `git-safe add <files>`.
2. Calls `git-safe-commit "<summary>" --no-work-item --body "..."`.

The tool then quietly runs `git add -A` and commits everything that's untracked or modified. The skill tells the agent to stage files first, so the agent reasonably assumes only those files will be committed.

Who hits it: any agent or principal whose repo has untracked content. That's common in a principal's `~/code` root, in monorepos, and in worktrees holding scratch files.

Impact: it can commit large unrelated trees, secrets, `.env` files, browser profiles or other projects into history. Undoing that means rewriting history, which the framework's own rules forbid.

## Steps to Reproduce

1. In a repo with untracked, unrelated directories, stage one file: `./agency/tools/git-safe add path/to/file`.
2. Run `./agency/tools/git-safe-commit "feat: thing" --no-work-item --body "Details"`.
3. Watch the processes: `git add -A` runs, and the commit includes the whole tree.

## Diagnostic Evidence

Seen on 2026-10-13. The commit hung for more than 2 minutes, and the process tree showed:

```
26638  /bin/bash ./agency/tools/git-safe-commit feat: add ... --no-work-item --body ...
26978  └─ /Library/Developer/CommandLineTools/usr/bin/git add -A
```

The working tree held about 18 untracked top-level directories of other projects, including a Playwright browser profile. We killed the process before any commit was made, then re-ran with `--staged`, which committed exactly the 5 intended files.

Source:

```
agency/tools/git-safe-commit:585-589
    # Stage changes if not staged-only mode
    if [ "$STAGED_ONLY" = false ]; then
        log_step "Staging all changes"
        git add -A
    fi
```

```
$ ./agency/tools/git-safe-commit --help | grep -c -- --staged
0
```

The skill (`.claude/skills/git-safe-commit/SKILL.md`) has no mention of `--staged` and says "Stage any unstaged files that should be included", which implies staging is selective.

## Root Cause

The default is backwards for an agent-safety tool. Staging everything is the dangerous behavior, and right now it's what you get unless you pass an undocumented opt-out.

## Requested Behavior

1. **Make staged-only the default.** Commit exactly what's in the index. If nothing is staged, refuse with a clear message rather than staging everything.
2. If commit-all is kept, make it an explicit opt-in (`--all`). Also have it print the file list and refuse when untracked files are present, unless `--include-untracked` is passed.
3. Document the flags in `--help` and in the `/git-safe-commit` skill.
4. Optional: warn when the staged set is very large or crosses top-level directories that aren't part of the project.

## Why This Matters

This is the framework's only sanctioned commit path, and raw `git commit` is blocked. So every agent's commits go through it. A safe-by-name tool with an unsafe default is exactly the gap the Safe Tools family exists to close.

## Related

- Hit while committing the This Happened! prototype in the `opus-5-5-build-day` repo (commit `2bd11d0`, made with `--staged`).
- Companion friction: `cp-safe` refuses sources outside a git repo (filed separately).

## Response Log

_(append responses, comments, and state changes here as they occur)_

- **2026-10-13:** Filed via `agency-issue file`. Issue created at https://github.com/the-agency-ai/the-agency/issues/493
