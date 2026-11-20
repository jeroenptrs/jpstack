---
name: small-change
description: >-
  Use when the user asks for /small-change, or hands over a small bug, a
  visual issue, a screenshot, or a small tweak for the DeltaBlue web app.
  Takes the change from triage to a verified merge request in a worktree,
  reports back with screenshots, and merges after the user's approval. Stops
  on any API change.
---

# Small change

Small change takes a small change in the DeltaBlue web app from a report to a
merged merge request (MR). Most runs are fixes that need no API change and no
architecture. Those go straight from triage to an MR without a question to
the user.

A run may also take on a small tweak to existing behavior, or light
architectural work, such as extracting a shared component or moving state up
one level. Align with the user on the approach first when "Know when to ask
and when to go" says so, then run it through the same steps.

The one gate that every run has is the user's review of the verified MR. Merge
only after the user approves.

Load `writing-for-devs` for every reply, commit, and MR description. Load
`ui-design-guidelines` and `writing-for-ui` when the change touches layout or
strings. The repo's `AGENTS.md` and `CLAUDE.md` win over this skill where they
conflict, especially on branch names, commit format, scripts, tests, and
feature flags. Read them at the start of the run.

## Stop on any API change

If the change needs a change to a DeltaBlue API, stop before you write code.
This includes a new endpoint, a new field, a changed response, or a backend
behavior change. A frontend workaround that fakes the missing data counts too.

Tell the user what the API lacks and which endpoint it affects. Then ask:

1. Open a ticket for the API change and stop here?
2. Ship a frontend-only part now and leave the rest behind the ticket?
3. Stop here?

Recommend one option. Don't continue until the user picks one.

## Know when to ask and when to go

Ask the user before you implement when the change needs any of these:

- an architectural decision that the code and its sibling views don't settle
- a restructure of a feature, or of how a view works
- a substantial React rework, such as new state ownership, a new data flow,
  or a rewrite of a component tree
- a package that the repo doesn't have yet
- a major version bump that changes how the app uses a package

Ask once, with numbered decisions. Give each a recommendation and the reason.

Go straight to the MR in every other case. Updating an existing package within
its current major version needs no input. Neither does adding the dark mode
classes a container forgot, or fixing the same bug in three views instead of
one. When in doubt between asking and going, check whether a reviewer could
judge the change from the diff and the preview alone. If so, go.

## 1. Triage

Triage is read-only. Do it wherever the conversation starts, including the
main checkout, so a run that stops never leaves a worktree behind. Answer
these before you plan the change:

- **What.** For a bug, the observed behavior and the expected behavior. For a
  tweak, the current behavior and the wanted behavior. For a screenshot, name
  the view, the element, and the state, such as dark mode, an empty list, or a
  narrow viewport.
- **Where.** The file and component that render it. Reproduce it locally with
  Playwright when the cause isn't obvious from the code.
- **Why.** For a bug, the cause, not the symptom. A fix that needs a
  workaround on top of a workaround means the diagnosis is wrong. For a tweak,
  the reason the code works the way it does now, such as a sibling view it
  matches or a constraint it handles.
- **How far.** Whether the same cause, or the same pattern, shows up
  elsewhere. Search the codebase for the component, the class pattern, or the
  hook involved. A screenshot shows one view. The cause often lives in a
  shared component or a pattern copied into siblings.

Change the cause at its source. If the cause lives in a shared component,
change the component, and check each view that uses it.

If the user gives a Jira issue key or link, read the issue for context and
acceptance criteria. Most small changes have no Jira issue. Don't create one.

## 2. Research, when the change isn't obvious

Skip this phase when the triage already names the change. Otherwise, look at:

- sibling views that already solved the same problem, and copy their pattern
- the component library and the installed packages, before you add anything
- the package's changelog and upstream issues, when the bug may live in a
  dependency and an update may fix it

## 3. Decide the shape of the change

Pick one before you create a branch:

- **Plain change.** The default. A bug fix, or a tweak that ships to everyone
  when it merges.
- **Behind a feature flag.** Use a flag when the change alters how something
  already works, or adds a substantial part to an existing view, and internal
  stakeholders should see it before customers do. Use the repo's flag
  mechanism. The flag gates the new behavior only, not unrelated files. Say in
  the MR who should validate it and how to turn the flag on.
- **Ask first.** One of the cases in "Know when to ask and when to go"
  applies. Wait for the user's answer before step 4.
- **Stop.** The change needs an API change. Follow "Stop on any API change".

## 4. Set up the worktree and the branch

Work in a git worktree, never on the main checkout. T3 Code usually starts the
conversation in one already. To check, compare `git rev-parse --git-dir` with
`git rev-parse --git-common-dir`. If the two match, you are on the main
checkout. Create a worktree from a fresh `origin/main` with the harness's
worktree tool, or with `git worktree add`.

Name the branch after the repo's convention. Pick the type from what the
triage found: `fix` for a bug, `feat` for new or changed behavior, and
`refactor` for light architectural work. Without a convention:

- With a Jira issue: `{type}/{issueKey}`, and commits such as
  `fix({issueKey}): add dark mode classes to the usage card`.
- Without one: `{type}/{short-kebab-topic}`, and a scope from the area you
  touched.

If the worktree came with a generic branch name and nothing is pushed yet,
rename it with `git branch -m {newName}`.

## 5. Capture the before screenshots

Before your first edit, capture a screenshot of each state you will check in
step 7. Reuse the screenshots from a Playwright reproduction in step 1 when
they cover the same states.

A screenshot from the user counts as the before for the state it shows, if
the local app still shows the same thing. It does not cover the other states
and views from step 7. Capture those yourself. When a before can't run
locally, for example because it needs data that only exists in a deployed
environment, capture it on the deployed app and label where it came from.

If the repo's Playwright doesn't work, don't stop: skip the screenshots you
can't take, list them as not verified, and continue.

## 6. Implement

Keep the diff to the change. Match the code around it and the sibling views.
Don't refactor nearby code that works, unless the light architectural work you
aligned on is that refactor. Commit with a semantic message.

Add or update a test when the change touches logic or adds behavior. Follow
the repo's existing tests for that area. A change to classes or markup only
needs no new unit test, unless the repo already covers that view with
end-to-end or visual tests. In that case, update them.

Run the repo's gates before you push: typecheck, lint, the tests near the
change, and the build. Fix what fails.

## 7. Verify locally

Check the change in the running app with the local Playwright. Cover the state
the report showed, plus the states the change could affect:

- light mode and dark mode, for any visual change
- a narrow viewport and a wide one, for any layout change
- the empty, loading, and error states, when the component has them
- every other view from the triage that shares the cause

Capture an after screenshot of each check, in the same state as its before. If
a check can't run locally, note it for step 9.

## 8. Open the merge request

Push the branch and open an MR against `main`. Write the title as a semantic
commit subject, because the squash merge turns it into the commit on `main`.
The description holds:

- what was wrong or what was wanted, and why, in one or two sentences
- what the change does, and the other places it covered
- the before and after screenshots
- the feature flag and who should validate it, if there is one
- what you verified, and what you did not

## 9. Verify on the preview

When the MR pipeline completes, it deploys the build to a Cloudflare Pages
preview URL. Find the URL in the pipeline output or on the MR. Wait for the
pipeline without a sleep loop. Use the harness's MR watch tool when it has one.

If the pipeline fails, read the job log, fix the cause, and push again. Then
repeat the checks from step 7 on the preview URL, including the ones that
couldn't run locally. Fix what you find, push, and check again.

Without Playwright, handle the preview checks as step 5 describes.

## 10. Hand the gate to the user

Report in a few lines:

- the MR link and the preview URL
- the cause and the change, in one sentence each
- what you verified locally and on the preview
- what you did not verify, and why

Show the screenshots in the report itself, not only in the MR. Use the after
screenshots from the preview when the preview check ran, and the local ones
otherwise. Put each next to its before screenshot, and label the view and the
state, such as dark mode or a narrow viewport. If no verification could run
at all, say so and say why.

Then wait. The user judges the result. Never approve your own change.

## 11. Merge

Merge only after the user approves. Rebase onto `origin/main` first if `main`
moved, push, and wait for the pipeline to pass. Merge with a squash merge and
delete the source branch. Report the merge in one line.

If the user sends feedback instead, apply it, verify it the same way, and hand
the gate back.

## Use subagents only when they save time

Most runs need no subagents. Do the triage, the change, and the verification
yourself. Spawn a subagent when the work splits into independent parts that
take real time, for example:

- searching a large codebase for every place that shares the cause, while you
  start on the change
- researching an upstream bug or a package update while you reproduce the
  issue
- verifying several views on the preview at once

Give each subagent one self-contained brief: the goal, the files to read, the
worktree and branch, and what to return. Read what comes back and verify a
claim before you repeat it to the user.
