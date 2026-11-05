---
name: sync-with-main
description: "Trigger this if the user says 'Stay in sync with main'. A variant of sync-with-trunk that lands work on main through a squash-merged PR or MR."
---

# Sync with main

The user wants one development branch for this conversation, or for a set of conversations on the same work, that stays close to `main`. You pull `main` into the branch by rebasing, as `sync-with-trunk` does. You never push to `main` directly. Work reaches `main` through a pull request or merge request against `main`, merged with a squash merge.

The development branch is the trunk for this conversation. Subagents may branch off it, but only you, the agent the user asked to sync, integrate their work into it.

## Inputs

Read these from the user's message:

- `{issueKey}`: an issue key such as `WEB-123`, optional. The user may give one explicitly or share an issue link. If no key is given, work without one.
- The repositories in scope. Usually that is the current repository. If the work spans several repositories, each one gets its own development branch with the same name and its own PR or MR.

## Naming

To name the development branch, use `{type}/{issueKey}`, where `{type}` is one of `feat`, `fix`, `chore`, or `docs`. Without an issue key, use `{type}/{short-kebab-topic}`. If the user names an existing branch, use that branch.

Write semantic commits with the issue key as the scope: `feat({issueKey}): add preview toggle`. Without an issue key, pick a short scope from the area you touched. Keep commit subjects concise, one line, no trailing period.

The squash merge turns the PR or MR title into the commit on `main`. Write the title as a semantic commit subject in the same form.

## Find out how preview deploys work first

Before the first sync, read the `AGENTS.md` and `CLAUDE.md` files of every repository in scope, including nested ones, for how preview deploys work. Look for a deploy script, a CI job that deploys each PR or MR, or a command to run.

If the files don't say, ask the user once, at the start: "How do preview deploys work for this project, if there are any?" If the user doesn't answer, assume the project has no preview deploys and stop asking. Change that assumption only when the user mentions preview deploys later.

## Sync from main

Run this loop for every unit of work that is ready:

1. Commit on the development branch with a semantic commit.
2. Fetch and rebase the development branch onto `origin/main`:

   ```
   git fetch origin
   git rebase origin/main
   ```

   If the rebase conflicts, resolve the conflicts, continue the rebase, and run the project's checks before going on.
3. Push the development branch. The rebase rewrites its history, so use a lease. Never force-push `main`:

   ```
   git push --force-with-lease origin {devBranch}
   ```

   If the lease fails, another conversation or another person pushed to the development branch after your fetch. Fetch, rebase onto `origin/{devBranch}`, then go back to step 2.
4. If the pushed commits changed code and the project has preview deploys, trigger one the way the project describes. If the platform deploys every PR or MR on push, the push in step 3 is the trigger. Skip the deploy for docs-only or config-only commits.

## Sync into main

Open one PR or MR from the development branch against `main` after the first push, and keep it open while the work goes on. Each later push updates it. Use `gh` for GitHub and `glab` for GitLab.

Merge it only when the user asks you to. Merge with a squash merge, and delete the source branch afterwards:

```
gh pr merge {number} --squash --delete-branch
glab mr merge {number} --squash --remove-source-branch
```

Before merging, rebase onto `origin/main` once more, push, and wait for the required checks to pass.

The squash commit on `main` does not contain the development branch's commits, so a later rebase of the old branch conflicts with its own work. To continue after a merge, start a fresh development branch from `origin/main`, with a new name if the old branch still exists on the remote:

```
git fetch origin
git switch -c {newDevBranch} origin/main
```

## Integrate subagent work yourself

Subagents may work in their own worktrees and branches. Branch them from the current development branch. Give each one a branch name derived from the development branch, such as `{devBranch}--{task}`.

Subagents commit on their own branch and stop there. They never push to `main`, never push to the development branch, and never open a PR or MR.

When a subagent finishes, integrate its work into the development branch:

1. Rebase the subagent's branch onto the development branch.
2. Fast-forward the development branch to it with `git merge --ff-only {subagentBranch}`. If several subagents finish together, integrate them one at a time and rebase each onto the result of the previous one.
3. Run the project's checks on the development branch.
4. Remove the subagent's worktree and delete its branch.
5. Run the "Sync from main" loop from step 2.

If a subagent's work conflicts with the development branch and you can't resolve the conflict, stop and show the user the conflicting files.

## Report

After each sync, tell the user in one or two lines:

- the commit subjects that landed on the development branch
- the PR or MR link, and whether it is merged
- whether a preview deploy ran, or that the project has no preview deploys

If a rebase, a lease, or an integration failed and you could not resolve it, stop and show the conflicting files.
