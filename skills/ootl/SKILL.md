---
name: ootl
description: "Trigger this if the user says 'jog my memory' or 'I'm out of the loop, fill me in'. Catches the user up on the conversation in three layers."
---

# Out of the loop

The user is coming back to this conversation after a break. Fill them in with
three layers, in this order:

1. **Summary.** One short paragraph on what this conversation is about. Give
   only enough for the user to recall what you were working on together.
2. **Last turn.** A bullet list of what happened in the last turn and what
   shipped, such as commits, pushes, deploys, and changed files. Keep each
   bullet short and factual. Put the points the user can act on first.
3. **Open decisions.** A numbered list of every open decision and everything
   the user needs to look at. Put the highest-priority items and the items
   with the biggest gain first. If nothing is open, say so.

Write all three layers following `writing-for-devs`.
