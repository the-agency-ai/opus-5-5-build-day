---
report_type: agency-issue
issue_type: friction
filing_agent: jordan
filed_by: jordan
date_filed: 2026-10-13
target_repo: the-agency-ai/the-agency
github_issue: https://github.com/the-agency-ai/the-agency/issues/495
github_issue_number: 495
status: open
---

# No sanctioned way to seed main on a brand-new GitHub repo

**Filed:** 2026-10-13T08:40:00Z
**Target:** [the-agency-ai/the-agency](https://github.com/the-agency-ai/the-agency)
**Issue:** [#495](https://github.com/the-agency-ai/the-agency/issues/495)
**Type:** friction
**Status:** open

## Filed Body

**Type:** friction

## Problem

There is no sanctioned way to put `main` on a brand-new, empty GitHub repo.

- `agency/tools/git-push` and `git-captain push` both refuse `main` ("All changes reach main through PRs").
- A new repo has no `main` to open a PR against, so the PR flow can't start.
- Who hits it: any captain publishing a local agency repo to GitHub for the first time.
- Impact: the only path today is to go around the tools.

## Steps to Reproduce

1. `agency init` a local repo and commit on `main`. There is no remote.
2. Try to publish it to a new GitHub repo with the framework tools.
3. `./agency/tools/git-push main` → `BLOCKED: Cannot push to main. All changes reach main through PRs.`
4. `./agency/tools/git-captain push` has the same block. No tool creates the remote.

## Diagnostic Evidence

- Repo `opus-5-5-build-day`, 2026-10-13.
- The principal approved a one-time seed:
  `gh repo create the-agency-ai/opus-5-5-build-day --public --source . --remote origin --push`
- That was outside the framework tools, because none of them covers this case.

## Requested Behavior

A bootstrap verb, for example:

`git-captain publish-repo <owner/name> --public|--private [--description ...]`

It should:
- create the GitHub repo
- set `origin`
- push `main` exactly once
- set `issues.github.target_repo` in `agency.yaml` if it's empty
- **refuse if the remote already exists and isn't empty**

After that, everything goes through PRs as usual.

## Why This Matters

Publishing a repo is a normal first step. Making it a bypass teaches agents to route around the tools, which the framework is trying to prevent.

## Related friction (same session)

`pr-create` needs a QGR receipt and a `manifest.json` version bump even for docs-only or demo-only PRs. For example, `the-agency-ai/this-happened` with a PVR and A&D draft plus a prototype HTML.

A lighter gate for doc/prototype-only changes, perhaps an RGR-only path, would help.

## Response Log

_(append responses, comments, and state changes here as they occur)_

- **2026-10-13:** Filed via `agency-issue file`. Issue created at https://github.com/the-agency-ai/the-agency/issues/495
