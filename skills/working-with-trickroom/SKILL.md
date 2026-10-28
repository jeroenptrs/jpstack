---
name: working-with-trickroom
description: "Use when the user asks to design, sketch, draw, visualize, refine, or review UI in Trickroom, mentions boards, design files, or a design session, or asks to graduate a design into a component. Covers the Trickroom MCP workflow from setup to design-to-code handoff."
---

# Working with Trickroom

Trickroom is a local-first design tool. Designs are JSON files under `.trickroom/` in the project, built from React elements, design-system component instances, and Tailwind classes. Agents work on them through the Trickroom MCP server. The user judges visuals. Your job is to research, draw options, render them, and wait.

A design session has four phases. Analysis first, then design in Trickroom, then review rounds until the user approves, then code. Any implementation goes back to Trickroom for a check at the component level. Project files such as `CLAUDE.md` and `AGENTS.md` override this skill where they conflict.

## Before you draw

1. Install dependencies in the checkout you work in, for example `vp i` or `pnpm install`. Without them, screenshots and exports render broken. If a render looks wrong, suspect this first.
2. Run `project_list`. Pick the row whose project root is your working directory. In a worktree, call `project_select({ locationId })` for that row, or `project_select({ path })` to register it. The MCP autoselects the main checkout, so skipping this writes designs into the wrong checkout.
3. Call `guide` once for the core, then `guide({ designFileId })` for the file you work on, plus topics you need such as `operations`, `boards`, `recipes`, `components`, or `annotations`.
4. Call `memory_read({ designFileId })`. It returns project, system, and design notes as an index. Fetch bodies with `noteIds`. Memory notes win over this skill on conflict.
5. Call `design_list`, then `design_screenshot` the boards related to your task. Know what exists before you add to it.
6. Read the project's design-system docs and known-issues log if they exist, for example `docs/components.md` and `docs/trickroom-issues.md`.

## Research before design

Talk first, then act. Read the ticket, the current implementation, the legacy app, the API docs, and what peers in the market do. Then propose numbered decisions with a recommendation for each. The user answers with inline quotes or short go-aheads. Do not start drawing until the direction is clear.

## Where designs live

- Add boards to the existing design file for that surface. Create a new file only for a new surface or a separate exploration, and start it with `design_create({ name, from: { designFileId, elementId } })` so it copies an existing board.
- Name files and boards as `Area · Thing` and boards as `<Page> · <Tab> · <State>`. For proposals, letter them: `A · Sidebar holds sections`, `B · ...`.
- Record outcomes in the board name when a direction is chosen: `(not chosen)`, `(parked)`, `(flag on)`. Delete superseded option boards afterwards.
- Design files cannot be deleted. Rename an abandoned file to `Scratch — safe to delete`.

## Boards

- One board is one view or one state. Never one board per breakpoint. Check widths with `design_screenshot({ viewport: ["mobile", "tablet", "desktop"] })`. Show mobile and collapsed states when the user asks, on the same board.
- Give three to seven lettered options per open question, each a distinct idea with a one-line rationale and a recommendation. Expect redraws. "Draw it out, we need to see this to compare."
- Reuse what the code already has. Use published component instances and recipes from the project's system, not raw elements. Mirror an unpublished draft with raw elements named `<Component> (draft)` until it is published.
- Align with what is in code today. Reduce clutter rather than add it. Keep header chrome in line with the current implementation.
- Read the project's design-language invariants from its agent instructions before you draw, and follow them. Treat every visual correction the user gives in the session as an addition to that list for the rest of the session. No middle dots or dashes as separators in copy.

## Editing

- All edits go through `design_apply` with `expectedRevision` from your last read. Batches are atomic. On `REVISION_MISMATCH`, re-read only the boards it names and retry.
- Dry-run a batch with `design_validate` when it is large. Treat `UNSUPPORTED_PROP` as ambient noise. Gate on no new error classes.
- Large files make system component steps slow. Group operations into few batches.
- When the user says "this layer", call `editor_context`. After a visible change, call `editor_focus`. If either returns `no_server`, say so and continue with exports.
- Edit system component `group` and `description` in `components.json` directly. The MCP cannot change them.

## Show your work

- Export every board you touched with `design_export({ format: "png", destinationDir })` into a fresh folder such as `/tmp/<topic>-exports/<round>/`, and embed each image in the reply with markdown. Use `design_screenshot` for crops, viewports, and dark mode.
- Name the design file, its id, and each board next to its image. Say what changed per board in one or two lines.
- Look at your own captures before sending them. A board that does not match the canvas, a cut-off export, or a missing button fill is yours to fix first.
- Do not self-approve. Render, show, and stop.

## Feedback rounds

The user gives feedback in one of two ways. Treat both the same: apply, re-export, report.

- Chat feedback arrives as long dictated paragraphs or inline quote comments, usually scoped to one surface at a time. Work through every point in order and list what changed per point.
- Annotation rounds arrive in Trickroom. When the user says they sent a round, loop `annotation_watch({ round: true })` until it returns `closed`. Address each remark in its own `design_apply` with `addresses: [{ annotationId, reply }]`. Reply with `propose` when a remark needs a decision. Resolve a remark only when the user asks. Sends while a round is open belong to that round.

After each round, write a design memory note titled with the date, the ticket, and the status, for example `VIC-6388 refinement pass, review round 1 applied`. Update the plan doc under `docs/plans/` if the project keeps one.

## Graduate to components

When the user says a grouping should become a component, or a pattern appears three times:

1. Draft it with `component_draft_create`, from a design layer where possible.
2. Show the variant matrix with `design_screenshot({ component, matrix })`, including hover and active states when asked.
3. On explicit approval, `component_publish`, then run the project's codegen, for example `trickroom codegen .`, and write the hand-written wrapper. Never hand-edit generated files.
4. A component is done when `trickroom codegen . --check` exits 0, typecheck passes, and it renders in the docs app.

Vendor logos belong in the assets package, not the icon library.

## Commits and sync

- Commit designs as `chore({issueKey}): <summary>` and code as `feat({issueKey}): <summary>`, one chunk per commit. Commit and push only on the user's call.
- If the user said "Stay in sync with trunk", follow the `sync-with-trunk` skill: rebase, fast-forward the trunk, preview deploy when code changed.
- Concurrent sessions may share a tree. Stage by path. Never `git add -A` or `git stash`.

## Design to code

Only after the user approves the boards. Hand the implementation to a subagent or a new conversation with the ticket, branch, design file id, board names, and the sync rules. Reusable surfaces go into their own `components/<name>` package. After deploy, the user test-drives the preview and sends screenshots with one-line remarks. Fix, commit, sync, and redeploy per remark.

## Pitfalls seen in past sessions

- Exporting before dependencies were installed, three times.
- Reporting a board that did not match the canvas. Check the capture.
- Separate boards for desktop and mobile.
- Raw menus and buttons where a published instance exists.
- Logos in the icon package.
- Writing designs into the main checkout from a worktree.
- Chat copy and board names that use middle dots as separators in UI text.
