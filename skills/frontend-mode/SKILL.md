---
name: frontend-mode
description: >-
  Use when the user says to go into frontend mode, or asks for /frontend-mode.
  Runs a frontend feature from research, through a Trickroom design and review
  rounds, to an implementation and a preview deploy.
---

# Frontend mode

Frontend mode takes a feature from research to a preview deploy in five phases.
Some phases end at a gate. Don't start the next phase until that gate passes.

This skill sets the order of the work. Two other skills cover the details, so
load both at the start:

- `working-with-trickroom` for every design step. Frontend mode always uses
  Trickroom, because the approved boards are the spec for the implementation.
- `writing-for-devs` for every reply, brief, board name, and handoff.

When the user says to stay in sync with a trunk branch, also load
`sync-with-trunk`. Project files such as `AGENTS.md` and `CLAUDE.md` win over
this skill where they conflict.

## Pick models from the roster

Two agents work on a frontend mode run. The overseer is the agent the user
talks to. It does the research and the design, and it keeps the design context
for the whole run. The implementer writes the code in its own conversation.

| Role | Allowed | Excluded |
| --- | --- | --- |
| Overseer, research, and design | Fable 5.1 or newer, Opus 5.5 | Opus 5.0 |
| Implementation | Opus 5.5 | Opus 5.0 |

A model in the excluded column never gets that role, even when it is newer than
an allowed model. Update the table when a model ships or gets excluded. If
your own model is not allowed as overseer, say so before phase 1.

## 1. Research

Size the research to the feature. A copy change or a single control needs a
short pass. A new view or a new feature needs the full pass. Cover these sources
and cite each one in the findings:

- **The API.** Read the API docs and the spec. The API defines what the feature
  can do. Don't propose behavior that the API can't support. List each gap as an
  API gap, with the endpoint it affects.
- **Your own prior art.** Read the current implementation, the legacy app if one
  exists, and sibling views that solve a similar problem.
- **Peers and competitors.** Find out how comparable products solve the same
  problem. The feature often exists elsewhere already. Name the UX patterns that
  those products share. Say where the design should follow those patterns, and
  give the reason wherever it should differ.

End the phase with numbered decisions. Each decision gets a recommendation and
the reason for it.

**Gate.** The user picks a direction. Don't draw before that.

## 2. Design

Decide where the design goes before you draw:

- If no design file clearly owns the topic, create a new design file.
- If a design file covers the area, compare its scope with what you are about
  to draw. When the scope matches, add the work to that file. When the scope
  differs, start a new file. For example, a component or a subview gets its own
  file when the existing file holds only full views, and the reverse.
- Name the file you chose and the reason. The user may ask for a new file
  anyway, for example for a new feature inside a view that an existing file
  already covers. Follow that call.

Draw what the decisions need. That can be full views, a component, or only the
part of a view under discussion. Tie each option to the research, so the user
can see which API capability, prior art, or peer pattern it comes from.

For board setup, naming, and exports, follow `working-with-trickroom`.

## 3. Review rounds

Post the exports in the thread with one or two lines per board. Feedback
arrives in chat or as Trickroom annotations. Handle both the same way. Apply the
change, re-export, and report. Never approve your own visuals.

**Gate.** The user approves the boards.

## 4. Implementation

Start the implementer in a new top-level T3 Code conversation, on a model from
the implementation row. Send one self-contained brief that holds:

- the ticket and the branch
- the design file id and the names of the approved boards
- the API endpoints that the feature uses, and the API gaps from phase 1
- the prior-art references that the implementation must match
- the gates to run: tests, typecheck, lint, and build
- the trunk to sync with, if the user asked to stay in sync with one
- the return contract: the head SHA once committed, the preview URL once
  deployed, what was verified, what was not, and open questions

Commits and pushes depend on trunk sync:

- With trunk sync on, the implementer commits, pushes, and deploys as
  `sync-with-trunk` describes.
- Without trunk sync, the implementer stops before its first commit and reports
  back. Ask the user before anything is committed or pushed.

Check progress with a read-only subagent or by reading the implementer's
conversation. Compare the result with the approved boards, at the component
level in Trickroom where it applies. Send each difference back to the
implementer with the board name and what differs.

## 5. Preview deploy

When the gates pass and the result matches the boards, the implementer deploys
a preview. With trunk sync on, `sync-with-trunk` triggers the deploy. Without
it, the implementer runs the project's preview deploy script on the feature
branch after the user allows the commit and push. If the project has no
script, ask the user how it deploys.

The run ends at the preview deploy. Report the preview URL, the commits, the
push status, and what you did not verify. Show screenshots of the implemented
UI next to the approved boards.

## Feedback after the preview

The user decides when the feature is done. Expect more rounds after the first
preview. For each remark, decide whether it changes the design or only the
code. A design change goes through phases 2 and 3 before the implementer gets
it. A code-only change goes straight to the implementer. Redeploy after each
round.
