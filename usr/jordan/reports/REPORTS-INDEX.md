# Principal Reports Index

Reports filed externally (Anthropic Claude Code, the-agency GitHub, etc.) by or on behalf of jordan.

Each row is one filing. Click through to the per-report markdown for full context, current state, and response log.

**Location:** `usr/jordan/reports/` (principal-scoped)
**Naming convention:** `report-{kind}-{slug}-{YYYYMMDD}.md`

---

## Filed Reports

<!-- AGENCY-ISSUE-INDEX-START — agency-issue tool appends rows above the END marker -->

| Date | Title | Kind | Target | External ID | Local Report | Status |
|------|-------|------|--------|-------------|--------------|--------|

| 2026-10-13 | settings.json Write(**) permission rules no longer honored — migrate to Edit(**) | bug | the-agency-ai/the-agency | #491 | [report-agency-issue-settings-json-write-permission-rules-no-longer-hon-20261013](usr/jordan/reports/report-agency-issue-settings-json-write-permission-rules-no-longer-hon-20261013.md) | open |
| 2026-10-13 | Default model resolves to Opus 4.8 (1M) — should default to Claude 5 family (latest/most capable) | bug | the-agency-ai/the-agency | #492 | [report-agency-issue-default-model-resolves-to-opus-4-8-1m-should-defau-20261013](usr/jordan/reports/report-agency-issue-default-model-resolves-to-opus-4-8-1m-should-defau-20261013.md) | open |
| 2026-10-13 | git-safe-commit runs 'git add -A' by default and commits the whole working tree; --staged is undocumented | bug | the-agency-ai/the-agency | #493 | [report-agency-issue-git-safe-commit-runs-git-add-a-by-default-and-comm-20261013](usr/jordan/reports/report-agency-issue-git-safe-commit-runs-git-add-a-by-default-and-comm-20261013.md) | open |
| 2026-10-13 | cp-safe blocks copies from outside any git repo (scratchpad, /tmp) into the current worktree | friction | the-agency-ai/the-agency | #494 | [report-agency-issue-cp-safe-blocks-copies-from-outside-any-git-repo-sc-20261013](usr/jordan/reports/report-agency-issue-cp-safe-blocks-copies-from-outside-any-git-repo-sc-20261013.md) | open |
| 2026-10-13 | No sanctioned way to seed main on a brand-new GitHub repo | friction | the-agency-ai/the-agency | #495 | [report-agency-issue-no-sanctioned-way-to-seed-main-on-a-brand-new-gith-20261013](usr/jordan/reports/report-agency-issue-no-sanctioned-way-to-seed-main-on-a-brand-new-gith-20261013.md) | open |
<!-- AGENCY-ISSUE-INDEX-END -->

## How to add an entry

1. Draft the report with captain, following `agency/REFERENCE/FEEDBACK-FORMAT.md`
2. File via the appropriate channel:
   - `agency-issue file` → the-agency GitHub (auto-appends to this index)
   - `/feedback` → Anthropic Claude Code (manually create the report file + append a row)
3. The per-report markdown lives in this directory with full text, response log, and frontmatter linking back to the source seed
4. This index is the at-a-glance view; the per-report file is the source of truth

## Principle

Reports are the record of what we asked the outside world for. Every gap we find in an external tool that we can't close in userspace becomes a report. Every response from the other side gets appended to its report file so we have a full narrative.
