---
report_type: agency-issue
issue_type: bug
filing_agent: jordan
filed_by: jordan
date_filed: 2026-10-13
target_repo: the-agency-ai/the-agency
github_issue: https://github.com/the-agency-ai/the-agency/issues/492
github_issue_number: 492
status: open
---

# Default model resolves to Opus 4.8 (1M) — should default to Claude 5 family (latest/most capable)

**Filed:** 2026-10-13T06:33:13Z
**Target:** [the-agency-ai/the-agency](https://github.com/the-agency-ai/the-agency)
**Issue:** [#492](https://github.com/the-agency-ai/the-agency/issues/492)
**Type:** bug
**Status:** open

## Filed Body

**Type:** bug

## Problem

The framework's default model resolves to **Opus 4.8 (1M context)** — an older release — while the Claude 5 family (Opus 5.5, Sonnet 5.5, Haiku 5.5, Fable 5.1) is the most recent and most capable generation. This contradicts the framework's own stated guidance that agents should "default to the latest and most capable Claude models."

`agency/config/agency.yaml` sets:

```yaml
agents:
  default_model: "opus"
```

The bare `opus` alias currently resolves to Opus 4.8 (1M), not the latest Opus (Opus 5.5 / `claude-opus-5-5`). So every agent spawned at the default runs a generation behind what the framework recommends.

## Steps to Reproduce

1. Start a session in a repo using the framework default (`default_model: "opus"`).
2. Observe the running model / commit attribution: `claude-opus-4-8[1m]` ("Claude Opus 4.8 (1M context)").
3. Compare against the documented latest family (Opus 5.5 = `claude-opus-5-5`).

## Diagnostic Evidence

```
$ grep -n "default_model" agency/config/agency.yaml
20:  default_model: "opus"
```

Session environment reports: `claude-opus-4-8[1m]` / "Claude Opus 4.8 (1M context)".
Documented latest family (from CLAUDE.md environment block): Opus 5.5 `claude-opus-5-5`, Sonnet 5.5 `claude-sonnet-5-5`, Haiku 5.5 `claude-haiku-5-5`, Fable 5.1 `claude-fable-5-1`.

## Root Cause

`default_model` is pinned to the bare `opus` alias, which resolves to whatever the host maps "opus" to — here Opus 4.8 — rather than an explicit latest-generation id. The default is not tracking the "latest and most capable" principle.

## Requested Behavior

Update the framework default to a Claude 5 family model. Preferred: pin an explicit, current id rather than a bare alias so the default is unambiguous, e.g.:

```yaml
agents:
  default_model: "claude-opus-5-5"   # or the appropriate 5-family id
```

If a bare alias is intentionally preferred (so it auto-tracks the newest Opus), document that the alias is expected to resolve to the latest Opus and treat the current resolution to 4.8 as the bug to fix in the resolver.

## Why This Matters

Every agent spawned at the default inherits the model choice. Running a generation behind the documented "latest and most capable" default affects capability and consistency fleet-wide, and quietly diverges config from the framework's stated principle.

## Related

- CLAUDE.md environment block (Claude 5 family ids; "default to the latest and most capable Claude models")
- `agency/config/agency.yaml:20`

## Response Log

_(append responses, comments, and state changes here as they occur)_

- **2026-10-13:** Filed via `agency-issue file`. Issue created at https://github.com/the-agency-ai/the-agency/issues/492
