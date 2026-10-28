---
name: sync-with-trunk
description: "Trigger this if the user says 'Stay in sync with trunk {branchName}'"
---

# Sync with trunk

The user wants fast iteration on a shared trunk branch instead of the commit, push, merge request, merge flow. You rebase your feature branch on the trunk, fast-forward the trunk to your work, and trigger a preview deploy when code changed. The goal is to see progress on the trunk quickly and keep feature branches from drifting apart.

## Inputs

Read these from the user's message:

- `{branchName}`: the trunk branch, required. Taken from "Stay in sync with trunk {branchName}".
- `{issueKey}`: an issue key such as `WEB-123`, optional. The user may give one explicitly or share an issue link. If no key is given, work without one.

## Naming

To name the branch, use `{type}/{issueKey}`, where `{type}` is one of `feat`, `fix`, `chore`, or `docs`. Without an issue key, use `{type}/{short-kebab-topic}`.

Write semantic commits with the issue key as the scope: `feat({issueKey}): add preview toggle`. Without an issue key, pick a short scope from the area you touched. Keep commit subjects concise, one line, no trailing period.

## Procedure

Follow this loop for every unit of work that is ready:

1. Commit on the feature branch with a semantic commit.
2. Fetch and rebase the feature branch onto `origin/{branchName}`:

   ```
   git fetch origin
   git rebase origin/{branchName}
   ```

   If the rebase conflicts, resolve the conflicts, continue the rebase, and run the project's checks before going on.
3. Fast-forward the trunk to the feature branch and push. Never force-push the trunk and never create a merge commit:

   ```
   git checkout {branchName}
   git merge --ff-only {featureBranch}
   git push origin {branchName}
   git checkout {featureBranch}
   ```

   If the fast-forward fails, someone pushed to the trunk after your fetch. Go back to step 2.
4. If the merged commits changed code, trigger a preview deploy. Use the project's preview deploy script when one exists, otherwise ask the user how deploys are triggered. Skip the deploy for docs-only or config-only commits.

Do not open a merge request or a pull request for this branch unless the user asks for one.

## Report

After each sync, tell the user in one or two lines:

- the commit subjects that landed on `{branchName}`
- whether a preview deploy was triggered

If the rebase or fast-forward failed and you could not resolve it, stop and show the conflicting files.
