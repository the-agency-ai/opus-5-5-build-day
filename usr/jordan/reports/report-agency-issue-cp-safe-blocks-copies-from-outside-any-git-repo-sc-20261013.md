---
report_type: agency-issue
issue_type: friction
filing_agent: jordan
filed_by: jordan
date_filed: 2026-10-13
target_repo: the-agency-ai/the-agency
github_issue: https://github.com/the-agency-ai/the-agency/issues/494
github_issue_number: 494
status: open
---

# cp-safe blocks copies from outside any git repo (scratchpad, /tmp) into the current worktree

**Filed:** 2026-10-13T07:34:38Z
**Target:** [the-agency-ai/the-agency](https://github.com/the-agency-ai/the-agency)
**Issue:** [#494](https://github.com/the-agency-ai/the-agency/issues/494)
**Type:** friction
**Status:** open

## Filed Body

**Type:** friction

## Problem

`agency/tools/cp-safe` refuses to copy any source that isn't inside a git repository:

```
BLOCKED: Source is not in a git repository.
  Source: /private/tmp/claude-501/.../scratchpad/this-happened.html
```

`cp-safe` exists to block **cross-worktree** copies. Copying from outside any repo into the current one (from Claude Code's session scratchpad, `/tmp`, `~/Downloads`, or a generated build output) isn't cross-worktree, but it's blocked anyway. Raw `cp` is blocked too, so there's no sanctioned way to bring an external file into the repo.

Who hits it: any agent that builds something in the scratchpad, which Claude Code recommends for temporary files, and then wants to keep it. That covers prototypes, generated reports and downloaded fixtures.

## Steps to Reproduce

1. Create a file outside any git repo: `/tmp/x/file.html`.
2. Run `./agency/tools/cp-safe /tmp/x/file.html some/dir/file.html` from inside a repo.
3. It is blocked with "Source is not in a git repository."

## Diagnostic Evidence

`agency/tools/cp-safe:156-183` resolves the source's worktree with `git -C "$dir" rev-parse --show-toplevel`. When that fails, it treats the source as disallowed (line 183).

## Root Cause

"Couldn't resolve a worktree for the source" is treated the same as "the source is in another worktree." Only the second case is what the tool is meant to block.

## Requested Behavior

Allow copies whose source is outside every git repo, as long as the **destination** is inside the current worktree. Keep blocking sources inside a different worktree or repo. Optionally print a note such as "copying external file into worktree" for the telemetry trail.

## Why This Matters

Without a sanctioned path, agents fall back to `mv`, `cat >` or Python file copies. Those are exactly the hackarounds the telemetry loop wants to remove. Today I used `mv` from the scratchpad, which worked but shouldn't have been the only option.

## Related

- Hit while saving the This Happened! prototype into the `opus-5-5-build-day` repo (commit `2bd11d0`).
- Companion: `git-safe-commit` stages everything by default (filed separately).

## Response Log

_(append responses, comments, and state changes here as they occur)_

- **2026-10-13:** Filed via `agency-issue file`. Issue created at https://github.com/the-agency-ai/the-agency/issues/494
