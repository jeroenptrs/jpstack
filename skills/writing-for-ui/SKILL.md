---
name: writing-for-ui
description: "Writing guide for text inside a web app UI: labels, buttons, tooltips, empty states, errors, toasts, and dialogs. Use when writing, reviewing, porting, or translating UI copy. Not for marketing or website copy."
---

# Writing for UI

Text in a web app exists to help someone use it.
Every string competes with the data and controls around it, so most UI writing is deciding what not to write.

Follow `writing-for-devs` for sentence style.
For on-screen strings, this skill wins where the two conflict. Labels and short states can be fragments: "All logs loaded" and "No stack yet" are correct.

## Follow the product first

Infer each convention from the codebase before you write. If a convention is unclear, ask the user and store the decision in memory if possible.

This covers:

- Loading patterns, such as skeletons or spinners.
- Terminology, including the names of features and objects.
- Count formats, such as "Minio x3".
- Capitalization exceptions. Sentence case is the default.
- Punctuation in tooltips and helper text.

Never infer tone. Existing copy can be wrong, and legacy apps often are. When you port a string, rewrite it to the rules in this skill, even if the old string is the only example in the repo.

## Decide whether the text should exist

If it's clear, it doesn't need to be made even more clear. Check each string in this order:

1. If the title and controls already say it, cut it. A description under a "Logs" heading that says it shows logs is noise.
2. If the screen states the fact elsewhere, cut one. A bulk sheet doesn't need a count when its Edit button shows the count.
3. If structure can carry the meaning, use structure. A sort indicator in the table header replaces "Newest first".
4. If the detail is useful but secondary, move it behind a tooltip or a popover. "X of Y running" fits a tooltip, and a long list folds into three names plus "and X more".

Keep a hint only in these cases:

- The rule isn't guessable, such as the characters a key allows.
- Leaving would lose work.
- A new concept needs a very short descriptor, such as one line on a landing page or on a radio card.

Cutting text must not cut information:

- Icon-only controls keep an accessible name.
- A status dot keeps text for screen readers.
- Essential detail can't live only in a tooltip, because tooltips don't work on touch.

Run a deletion pass before handoff. For every string, ask what breaks if it goes. If nothing breaks, remove it.

## Write each element

**Names.** Use the user's noun, not the implementation's. "Log entries", not "lines". "Import", not "Import .env". Keep one name for one thing everywhere.

**Buttons.** Say what happens: "Save changes", not "Submit". Keep the verb through the flow, so "Publish" produces "Published".

**Errors.** Say what happened and how to fix it. Don't apologize, and never stop at "Something went wrong". "Could not launch {name}" plus the reason is enough.

**Empty states.** Point to the next action: "Add one by hand, or paste the variables you already have."

**Toasts.** Confirm the outcome, with at most one next step. "Change the order in the project settings." earns its place. "It is the last column on the board" doesn't.

**Disabled states.** Say why, or say what's finished: "Launch the application to change remote access.", "All logs loaded".

**Status text.** Keep it true. Show names instead of raw ids, and show one message per outcome. A partial failure names what was saved and what was kept, and never reports both "nothing was saved" and "sent".

**Destructive actions.** Match the friction to the impact:

- For a reversible action, offer undo and skip the confirmation.
- For a single resource, use a simple dialog.
- For wide impact, such as a whole repo or organization, use type-to-confirm in a danger zone.

State the consequence only when the action can't be undone. Confirm buttons name the action: "Delete repository", not "Yes". Titles never say "Are you sure?".

**Fit.** Test every string with the longest real name, with its plurals, and in the longest supported language.

## Keep the tone professional

This is a work tool, and someone may read the message while production is down. Read each string from that position.

- Don't add personality. That means no jokes, themes, puns, greetings, emoticons, or mood.
- Don't reassure or promise speed. Say what happens, or give the real number.
- Never use an exclamation mark on errors, warnings, empty states, or labels. A rare success message can have one.
- Avoid "we" as a character. The interface reports what happened: "Activation email sent", not "We have sent an activation email".
- Professional means plain, not stiff. "Invalid expression!" becomes "Enter a valid cron expression."

## Translate only when the product does

1. Check whether this section applies. Look for a message catalog and more than one locale in the target webapp. If the app has one locale, skip this section.
2. Write translatable strings. Add them to the catalog, never hard-code them, and don't build sentences by concatenation. Use the catalog's plural support.
3. Decide each term in this order:
   1. Reuse the term already in the catalog.
   2. Search how comparable products translate it. For a cloud console, compare competing consoles.
   3. If the choice is still unclear, ask the user whether to translate literally or keep the jargon. Show what the comparable products do.
4. Record the decision in a glossary, so the next agent doesn't search again.
5. Tell the user which translations nobody has reviewed.

## Patterns to detect and fix

1. **Restated UI.** A description that repeats its title or control. Delete it.
2. **Repeated facts.** "x ports on y stacks" above a table of ports, or "No applications" above an empty state that says the same. Keep one.
3. **Implementation nouns.** "Webhook config" becomes "notifications". "Log file" becomes "Log type". Name what the user manages.
4. **Vague actions.** "Submit", "OK", and "Yes" become the action: "Save changes", "Delete repository".
5. **Apologizing errors.** "Sorry, something went wrong" becomes what happened and how to fix it.
6. **Separators.** Never use `·` or `—` as a separator. Use spacing or a Separator component in layouts, and ` - ` in strings such as window titles.
7. **Dashes in ranges.** "A – B UTC" becomes "A to B UTC".
8. **Template chrome.** All-caps eyebrow labels, labels above content that add nothing, and `→` in link text. Use an icon for the arrow.
9. **Incidental toast facts.** Anything in a toast that isn't the outcome or the next step. Delete it.
10. **Lost information.** A removal that took an accessible name, or the only copy of a fact, with it. Restore the fact in the accessible name or a persistent place.
11. **Personality and themes.** "Houston, we have a problem..." becomes "Could not launch {name}". "We have a liftoff!" becomes "{name} launching". "No, abort mission" becomes "Cancel".
12. **Chatty asides.** "Almost there.. Press ENTER or click the magnifying glass to search! :)" becomes "Press Enter to search." "It looks empty in here." becomes "No projects yet."
13. **Speed promises and reassurance.** "It will be finished shortly", "in a jiffy", "hang tight". State what happened: "Unmount scheduled".
14. **Shouting.** "No containers!" becomes "No containers". "This can't be undone!" becomes "This can't be undone."
15. **"Successfully".** "Task was deleted successfully!" becomes "Task deleted". The message already says it worked.
