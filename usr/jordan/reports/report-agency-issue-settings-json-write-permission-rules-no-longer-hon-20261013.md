---
report_type: agency-issue
issue_type: bug
filing_agent: jordan
filed_by: jordan
date_filed: 2026-10-13
target_repo: the-agency-ai/the-agency
github_issue: https://github.com/the-agency-ai/the-agency/issues/491
github_issue_number: 491
status: open
---

# settings.json Write(**) permission rules no longer honored — migrate to Edit(**)

**Filed:** 2026-10-13T06:31:21Z
**Target:** [the-agency-ai/the-agency](https://github.com/the-agency-ai/the-agency)
**Issue:** [#491](https://github.com/the-agency-ai/the-agency/issues/491)
**Type:** bug
**Status:** open

## Filed Body

**Type:** bug

## Problem

The framework-shipped `.claude/settings.json` declares permission allow rules using `Write(...)` globs. On `claude` startup, Claude Code now rejects these — file permission checks only honor `Edit(path)` rules (which cover all file-editing tools, including Write). Every agency session therefore opens with four warning lines, and the intended write-allowlist is silently inert:

```
Permission allow rule (.claude/settings.json): Write(**) is not matched by file permission checks — only Edit(path) rules are. Use Edit(**) instead (Edit rules cover all file-editing tools).
Permission allow rule (.claude/settings.json): Write(.claude/skills/**) is not matched by file permission checks — only Edit(path) rules are. Use Edit(.claude/skills/**) instead (Edit rules cover all file-editing tools).
Permission allow rule (.claude/settings.json): Write(/tmp/**) is not matched by file permission checks — only Edit(path) rules are. Use Edit(/tmp/**) instead (Edit rules cover all file-editing tools).
Permission allow rule (.claude/settings.json): Write(/var/folders/**) is not matched by file permission checks — only Edit(path) rules are. Use Edit(/var/folders/**) instead (Edit rules cover all file-editing tools).
```

Impact: every principal/agent on a current Claude Code sees startup noise, and the Write allowlist no longer takes effect — meaning Write calls that were meant to be pre-approved now prompt.

## Steps to Reproduce

1. Use a repo that ships the framework `.claude/settings.json` (contains `Write(...)` permission allow rules).
2. Launch `claude` on a current Claude Code build.
3. Observe the four warnings; the Write rules are not applied.

## Diagnostic Evidence

```
$ grep -n "Write(" .claude/settings.json
67:      "Write(**)",
69:      "Write(.claude/skills/**)",
71:      "Write(/tmp/**)",
74:      "Write(/var/folders/**)",

$ git ls-files .claude/settings.json
.claude/settings.json   # framework-tracked file
```

## Root Cause

Claude Code file permission checks only match `Edit(path)` rules; `Edit` rules cover all file-editing tools (Write included). The framework's `.claude/settings.json` was written against the older `Write(...)` form, which is no longer honored.

## Requested Behavior

Migrate the `Write(...)` permission allow rules in the framework's `.claude/settings.json` to `Edit(...)`:

- `Write(**)` → `Edit(**)`
- `Write(.claude/skills/**)` → `Edit(.claude/skills/**)`
- `Write(/tmp/**)` → `Edit(/tmp/**)`
- `Write(/var/folders/**)` → `Edit(/var/folders/**)`

Also audit any other `settings.json` templates / scaffolds in the framework (and the `update-config`-style generators, if any) that emit `Write(...)` rules, so new repos are scaffolded with `Edit(...)`.

## Why This Matters

Clean startup with no warnings is a baseline expectation; a silently-inert write allowlist means pre-approved writes start prompting again, adding friction to every session across the fleet.

## Related

- Local bug report: `agency/workstreams/housekeeping/bugs/BUG-0001.md`

## Response Log

_(append responses, comments, and state changes here as they occur)_

- **2026-10-13:** Filed via `agency-issue file`. Issue created at https://github.com/the-agency-ai/the-agency/issues/491
