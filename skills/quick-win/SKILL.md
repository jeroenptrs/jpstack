---
name: quick-win
description: "Trigger this if the user asks 'What's a quick win' about a feature, the current conversation, or a specific thread. Names the one next action with the biggest gain for the least effort."
---

# Quick win

The user wants one next action, not a plan. Pick the action with the biggest
gain. When two actions have about the same gain, pick the one that takes less
effort.

## Find the candidates

1. Set the scope. If the user names a thread, read it first. If the user names
   a feature, check its branch, its PR or MR, and its ticket. Otherwise, use the
   current conversation.
2. List what is still open in that scope: open decisions, failing checks,
   unanswered review comments, unfinished steps, and known bugs.
3. Rank the candidates by gain first and effort second. From the most gain to
   the least: it unblocks other work, it fixes something the user can see, it
   removes a risk, it cleans up. Effort is low when you can finish the action in
   one turn without input from anyone.

## Answer

Reply in at most four lines:

- **Action.** One sentence, written as a command, that says what to do and
  where. Name the file, PR, board, or decision.
- **Gain.** One sentence on what the action unblocks or fixes.
- **Effort.** The size in concrete units, such as "one file" or "one decision
  from you".
- **Runner-up.** One line, and only when a second option comes close.

If every candidate is blocked or large, say so. Then name the smallest step
that removes the block.

Don't start the work. Offer to do it, then wait for the user's go.

Write the answer following `writing-for-devs`.
