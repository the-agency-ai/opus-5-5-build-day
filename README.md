# Opus 5.5 Build Day

One day (2026-10-13) building **This Happened!**, a working prototype of in-app problem reporting that an AI agent can act on. Built with Claude Code (Claude Opus 5.5) and [The Agency](agency/README-THEAGENCY.md) framework.

**Try it:** [This Happened! prototype](https://claude.ai/artifact/M3mKd5MsGbdf4iH3xtVfo6) (opens in claude.ai).

## The idea

When something breaks in an app, people have to describe it from memory. The team that fixes it needs the steps, the network calls, the logs, and what the user actually saw. This Happened! closes that gap:

- **Tap !** to report a problem. The app has already been recording what happened. You see exactly what will be sent, with tokens and personal details redacted in your browser, and nothing leaves until you press Send.
- **Or tell the assistant**, "It never arrived". It admits what went wrong and offers to file the report for you.
- **Follow it to a fix** in *My reports*: Received → Looking into it → Fix on the way → Fixed, in plain language, with a question from the team if they need one.
- **An agent picks it up.** Claude Code gets the redacted bundle and a **Breadcrumb trail**: every hop of each request gets a time-ordered UUID7 that points at its parent, so the agent can follow a failure from the button click to the service that timed out. It triages, reproduces, writes tests and opens a draft PR. A person approves the merge. Triage never marks anything Fixed: if a report looks like one that's already fixed, a person decides.
- **Tap ?** for **How Do I**: ask how to do something, then *Show me* (a spotlight walks you through the real controls) or *Do it for me* (a visible cursor does it, asks before any change, and offers Undo). If it can't help, "This didn't work" turns into a report.

## The prototype

[`agency/workstreams/opus-5-5-build-day/prototypes/this-happened.html`](agency/workstreams/opus-5-5-build-day/prototypes/this-happened.html) is one self-contained file, with no build step. It has three tabs, which are three views of the same report:

| Tab | What it shows |
|---|---|
| **Report it** | *Tallyho*, a made-up invoicing app with an embedded assistant and the **! ?** controls |
| **My reports** | What the reporter sees: status, updates, the agent's question, confirm the fix |
| **Agent queue** | What Claude Code sees: the bundle, the Breadcrumb waterfall, the triage, the agent session |

**Live vs scripted.**
- *In claude.ai:* the Agent queue triage and How Do I answers are real Claude calls, made on the viewer's own account through the artifact's `sample` capability.
- *Anywhere else* (for example opening the file locally): a scripted run stands in, so the demo always works offline.

**What's simulated:** Tallyho, its backend and the Breadcrumb trails are generated in the page. The code paths and PR numbers the agent mentions are made up.

### The export bug used as the example

Exporting a large month makes the PDF renderer time out after 30 s. Through the assistant, it's worse: the assistant says "Done! I've emailed them" even though the export tool failed. Both paths lead to a report, and the trail shows exactly where it broke.

## What else came out of the day

- **Product docs:** the product requirements and the architecture & design drafts for This Happened!, written from this prototype and an earlier planning session. They live in the product repo, [the-agency-ai/this-happened](https://github.com/the-agency-ai/this-happened):
  - PVR: [in the repo](https://github.com/the-agency-ai/this-happened/blob/main/agency/workstreams/this-happened/pvr-this-happened-rev1-20261013.md), or [comment on the review copy](https://claude.ai/artifact/WfWqxNNLtcoG2SLJd6uwEa)
  - A&D: [in the repo](https://github.com/the-agency-ai/this-happened/blob/main/agency/workstreams/this-happened/ad-this-happened-rev1-20261013.md), or [comment on the review copy](https://claude.ai/artifact/UiyZzns17R5WQrAKRMtUee)
- **Tests:** a jsdom test suite for the prototype (517 checks) is in the same repo under [`prototypes/tests/`](https://github.com/the-agency-ai/this-happened/tree/main/agency/workstreams/this-happened/prototypes/tests). Run it with `npm install && npm test`.
- **Quality gate:** before release, the prototype and docs went through four parallel reviewers (code, security, design, tests). All 41 findings were fixed ([release v46.25](https://github.com/the-agency-ai/this-happened/releases/tag/v46.25)).
- **Framework feedback:** five issues filed against The Agency while using it: [#491](https://github.com/the-agency-ai/the-agency/issues/491), [#492](https://github.com/the-agency-ai/the-agency/issues/492), [#493](https://github.com/the-agency-ai/the-agency/issues/493), [#494](https://github.com/the-agency-ai/the-agency/issues/494), [#495](https://github.com/the-agency-ai/the-agency/issues/495). Local copies are in [`usr/jordan/reports/`](usr/jordan/reports/).

## Repo layout

| Path | What's there |
|---|---|
| `agency/workstreams/opus-5-5-build-day/prototypes/` | The prototype |
| `agency/` | The Agency framework: tools, skills, hooks, reference docs |
| `usr/jordan/` | Principal sandbox: session handoffs and filed issue reports |
| `CLAUDE.md` | Instructions Claude Code loads in this repo |

## How it was built

Claude Code ran as the Agency *captain*. The parallel parts ran as multi-agent workflows: one agent built live triage while another built How Do I on a separate copy of the file, and the two were merged with `diff3`. Product docs and the quality-gate reviewers also ran as separate agents. Every change went through the framework's safe git tools and commit wrappers.
