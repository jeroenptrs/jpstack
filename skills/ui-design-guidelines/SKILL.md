---
name: ui-design-guidelines
description: "Design principles and process for web app UI: what to show, hierarchy, surfaces, lists, chips, menus, sheets, dialogs, states, where settings live, and interaction. Use when designing a screen, implementing or reviewing a view, or porting a legacy screen. Works together with writing-for-ui, which owns every string, and with the project's own design conventions."
---

# UI design guidelines

A screen in a work tool exists to show what is going on and to offer the next
action. Most design work is deciding what not to draw.

Three layers divide the work:

- `writing-for-ui` owns every string: labels, hints, errors, toasts, counts,
  separators in text. When a rule here touches wording, that skill wins.
- The project's own conventions (its design system, its reference views, its
  recorded decisions) own the specifics: token names, sizes, which view is
  the canon for which pattern. Look for a project skill or an agents file
  before you design, and follow it where it is more specific than this one.
- This skill owns the principles and the process that hold across projects.

Examples below come from real design reviews. Treat them as illustrations
of the principle, not as a project's spec.

## Process

1. **Research before drawing.** Read the API, so you never propose what it
   can't support. Read the current implementation, the legacy screen if one
   exists, and the sibling views that solve a similar problem. Look at how
   comparable products solve it, and say where you follow them and where you
   differ and why. End with numbered decisions, each with a recommendation.
2. **Name the pattern you are reusing.** Before a new table, header, menu,
   or settings page, point at the existing view it copies. "That is how we do
   tables" was the whole correction when a list used clickable titles
   instead of rows. If two views disagree, ask which one is the canon.
3. **Decide what not to draw.** For every element ask what the user does
   with it. If nothing, cut it. The user's rule: "if it's clear, it doesn't
   need to be made even more clear."
4. **Give every fact one home.** Decide where a piece of data is edited and
   where it is only shown. Project data belongs on the project's settings,
   not in a create dialog and not in a user preference. A hint about where to
   edit it can go in a success toast.
5. **Draw every state.** Loading, empty, no results, error, partial, and the
   transitional states. An empty board is not a design.
6. **One responsive board per state.** Draw the narrow layout as a
   breakpoint of the same board, never as a second board. Check it at the
   narrowest and widest supported widths.
7. **Render, then stop and ask.** Visual sign-off belongs to the user.
   Share screenshots of the boards and never self-approve.
8. **Keep design and code together.** When the code changes a designed
   screen, update the board in the same change. A change to a shared
   primitive's contract goes through the design tool first, then becomes a
   shared component, then reaches the views.

## Show only what exists

- Absence is not a status. Don't render ghost rows, placeholder cards, or
  disabled stand-ins for things that are not there. A legacy board showed a
  stub row per environment for applications that could be cloned there; the
  redesign lists only real applications.
- Filter out what doesn't apply to the current scope. Don't grey it out.
- Leave a row out when its source is missing, instead of inventing a value.
- Unknown shows as unknown, not as off or disabled.
- Don't draw a metric the API can't back yet. If the ideal needs an API
  change, draw the current version and the ideal version separately and turn
  the difference into an API wishlist.
- Don't let the UI claim something the system doesn't do. "Applied on next
  start" was cut because it was not true.
- Don't keep client-only state that someone else could change elsewhere.
  Pending changes live in memory, not in local storage.
- Don't advertise internals or half-features: no debug switch, no upstream
  feature the product doesn't support.
- Hide an action whose precondition fails, or disable it with a tooltip that
  says why. Never let the API fail on click.
- An identifier earns a row only when people paste it somewhere.

## Build hierarchy with size, not colour

- Colour encodes role, not rank. Default text keeps the normal ink, and a
  smaller size is the hierarchy.
- Only two roles may dim: labels attached to a control or a column, and
  secondary meta such as hints, placeholders, and the line under a name.
  Nothing else dims. A running count on a column header and a pin icon in a
  row were both corrected back to the default colour.
- Titles, subtitles, section headings, and joining words never dim.
- Monospace is for refs, ids, and values. A plain word or an empty
  placeholder renders in the body font.
- A toast title may take the type colour. Its description stays regular.
- Siblings share one styling. Two links in one toolbar, or two section
  eyebrows in one view, get the same size and tone.
- Every colour you set needs its dark-mode counterpart. A hover colour that
  "looks really janky in dark mode" is a bug, not a theme gap.
- Truncate a title only when it is really out of room.

## Keep headers lean

- A detail header is a status dot, the name, and the tabs. "I just wanna see
  the dot, name and tabs for now, there's so much overkill in the badges."
- No metadata line under the name, no count line, no overflow menu in a
  header. An action that follows the status, such as Start and Stop, may sit
  there.
- Don't repeat in a header what a tab or toolbar already shows.
- Keep the header's secondary column one line tall across sibling views so
  the title row never jumps when the user switches. Detail goes behind a
  small info button.
- Opening an item lands on its overview, never on its settings.

## Surfaces, borders, and spacing

- Borders mean scope, shadows mean elevation. Every opaque surface carries
  one of them.
- An inset shadow works on a static card. A real border is needed when a
  full-width child changes background: hoverable rows, expandable rows, an
  embed. "Inset shadow is desired unless there's interactivity in play."
- One divider between adjacent surfaces. A footer under a bordered table
  drops its own top border. The last row draws no separator.
- No card in card in card. A section heading sits on the page background and
  the items are the surface.
- Related rows are one surface with dividers, not separate floating cards.
- Fix height mismatches through typography and padding first. A button
  taller than its input is aligned by giving both one text size, not a fixed
  height.
- A horizontally scrolling board gets the same end gap as its start gap.
- Content reaches a divider the same way sibling views do. A "padding chin"
  before a divider is a bug.

## Lists, tables, and cards

- The row is the click target. Hover changes the background; the name never
  underlines. "The card should just have a hover state, no text decoration."
- Rows open a sheet. No action column, no per-row overflow menu. When a row
  keeps one inline action, it is an icon button with a tooltip at a fixed
  position across rows.
- Row actions are always visible, never revealed on hover.
- A destructive action never sits in a row or card menu. It lives on the
  item's settings page; the menu may link there.
- A trailing chevron means a child page. A trailing arrow means navigation
  elsewhere. A row that opens a sheet ends in neither.
- Long lists scroll infinitely with virtualization. No "load more" footer,
  and no loading every page before the first paint. A new table takes the
  full stack from day one: virtualized rows, debounced search, keyboard
  navigation, selection even before bulk actions exist.
- The page scrolls, not the list. A list page scrolls from the whole content
  pane, gutters included, with a sticky column header. A wide table puts its
  horizontal scrollbar at the page edge.
- Columns fit; the page never scrolls sideways. Stage columns by breakpoint
  and keep status, name, and the row menu at the narrowest width.
- A card that lists resources is not a table: name over one meta line, no
  column heads.
- Toolbar: filter on the left, the primary action at the right end.
  Secondary create paths fold into a split button on the primary action. A
  selection action moves next to the main apply action, never onto its own
  row.
- Card-level actions never become a row. A single action sits in the card
  header on the right. Several actions move to a footer strip along the
  bottom of the card, right-aligned.
- Group by the parent the user thinks in, and put the add action on the
  group, prefilled, instead of one big add button.
- Mark the active one of several definitions with a trailing icon, not a
  strikethrough on the others.
- Bulk edit shows the selected items as a plain list with a per-row preview
  of what changes, not a summary count.
- One drag pattern. The handle that sorts one list sorts every list.

## Chips, badges, and icons

- Status at rest is the dot. A status word appears only when the thing is
  not in its normal state, and never as a badge on a card.
- Cap the fact chips on a card or column at three, each with a tooltip.
  Show what is on and what is exceptional; move the rest behind an info
  button.
- One glyph per chip. A state change swaps the glyph. No chevrons on chips.
- Fold a related state into the chip that owns the fact instead of adding a
  badge. Version drift became a tone and glyph change on the existing
  lineage chip, with the action inside its popover.
- A count chip is a link when it has one target and a popover of links when
  it has several.
- Keep chip metrics identical across neighbours. A badge two pixels shorter
  than its neighbours was a bug.
- Pick the appearance by meaning and keep it consistent within a list.
  Outline for facts, soft for neutral attributes, strong only for emphasis.
  A soft neutral badge vanishes on a selected row.
- A link is a text button with a link glyph, not a chip. A text link to
  settings became an icon button with the diagonal arrow.
- Instance rows carry no leading icon. Items the user recognises by name
  stay text-only; a section that holds one type puts the type icon in its
  header. Navigation and action rows keep their icon.
- Brand marks are assets, not icons. Inside a toned chip they draw in the
  current colour so the tone colours them; on an empty state they sit on a
  neutral chip, not the accent. Check them in dark mode.
- Icon size follows the host element, and icon colour follows the text
  through hover and active.

## Popovers, menus, and tooltips

- Build entity menus like the product's switcher: a group label as title,
  rows that are the link, label left, meta right. No leading icons, no
  trailing buttons, no footer call to action.
- A row that opens a site ends in the link glyph. Drop secondary detail when
  the row gets crowded.
- A trigger that opens a popover has a hover state, and so do the rows.
- A long menu caps its height to the viewport, with the persistent items
  (clear, remove) outside the scroll area so they stay reachable.
- Fold long selections: a few names in full, then three names plus "and N
  more" with an info button that opens a scrollable list.
- Info buttons open popovers on click. Chips get tooltips on hover.
- A tooltip carries one fact per line, and secondary facts about a key sit
  behind an info icon next to the key, not in the row.
- Text never overflows a popup. Truncate or wrap inside it.

## Sheets, dialogs, and forms

- A dialog holds a short form of a few fields. A sheet holds the detail or
  inspector of one item out of a list. A large editor is a page.
- Confirm at commit time, not per toggle. Turning off one option in a row
  doesn't open a dialog; saving does.
- No confirmation for an action that only starts something or can be
  undone. Offer undo in the toast. Stopping and restarting open the real
  confirm dialog with focus on Cancel.
- A destructive choice inside a form changes the commit button: tone to
  danger, label to the action. Mark only the rows the choice affects.
- Mutually exclusive actions sit side by side as one choice, not a form plus
  a danger zone far below.
- Validate on blur, not per keystroke, and don't disable the submit button on
  field errors, because a disabled button can't be clicked to blur the
  field. Show a backend error inside the dialog, above the footer.
- A pending state is an indicator on the apply button, not a banner.
- Group an import's inputs in one card. The drop zone covers the whole field
  with a dashed border and the standard "drag a file here, or browse" hint,
  and a file picker adds to the paste path rather than replacing it.
- A compound control looks like one control: one shared outline, one
  internal divider, focus on the whole, press on the segment.
- A switch applies at once; a checkbox submits with the form. An action that
  is asynchronous and can fail is a button, not a switch.
- An action that needs confirmation leaves the command palette and opens
  the real dialog.

## States

- Always have a skeleton. It mirrors the final layout, keeps the chrome and
  height, and turns toolbar buttons into skeletons too. Never a loading
  sentence, never live buttons beside a skeleton table. Key it on an
  in-flight request, so an idle query hides the tile instead of showing a
  skeleton forever.
- An empty result is an empty state, never an error. An empty collection
  shows the primary action once, in the empty state, not also in the
  toolbar. No results in a filtered list use the compact variant.
- Every failure keeps a retry. A failed secondary request never hides the
  main content and never renders as zero.
- Every card keeps its footprint across loading, error, and no-data so the
  grid never jumps. Refetching holds the old content at reduced opacity.
- Live charts render the full window from the first frame and let samples
  fill in. Never fabricate history.
- Disabled-but-visible is the placeholder for an action about to get a
  function. Don't stub sections that have no near-term function.
- Preserve context through routine updates: saving keeps the selection,
  renaming doesn't shift the header, closing a mouse-opened menu hands the
  arrow keys back to the list.

## Settings, preferences, and flags

- One home per fact (see the process). Sizing is configuration, not settings.
- A display preference is one per-user object for the whole app. "Per user
  fine, per board absolutely not." It sits behind an icon-only button next to
  the filter, with a marker when a value is off its default. A dedicated
  preferences page appears only when several views have options.
- A settings page is a stack of cards with the label left and the control
  right; the danger zone comes last. Descriptions follow `writing-for-ui`.
- When another view manages a setting, the settings card links there: one
  row per item with a state badge and a trailing arrow. Hide the card when
  nothing applies.
- Non-destructive copies (clone, duplicate, save as template) group with the
  item's relations, never in the danger zone.
- A feature flag gates whether an option is offered, not how it renders. A
  new surface ships behind a kill switch that defaults to on; a removal flag
  defaults to off; a flag owns a whole column, heading and width included.
  Never hide controls while flags are still loading.

## Charts

- Pick line or bar per chart from what the series measure. Default to
  straight unsmoothed lines with no fill. Stack bars only for parts of a
  whole. Keep the same type in overview and detail.
- Legends show names only. Values live in the tooltip and the detail table.
- Ticks are round numbers in the display unit. Years are written in full.
- Every chart card has one expand action to a larger detail view.
- Embedded third-party charts keep the card header and drop only the padded
  body.

## Interaction and keyboard

- Drag and drop is an accelerator. Every workflow completes without it, and
  a drop opens the same flow as the visible action.
- Drop targets are unmistakable: a solid tint, dashed border, and centred
  hint over the whole target, not a faint wash.
- A card can be both draggable and clickable: click opens, drag moves.
- Lists share one key map: arrows move, Enter opens, a key focuses search,
  Escape clears. Every tabbed shell gets a key sequence per tab. Every new
  shortcut appears in the shortcuts overlay, and hints show only for
  commands that work on the current page.
- A command palette stays anchored under its trigger. Centred "will look
  atrocious". It lists each entity once.
- A full-screen view fills exactly one viewport at every text scale, with
  the footer pinned and the list taking the rest.

## Mobile and responsive

- Nothing scrolls sideways at page level. Tab strips scroll inside their own
  container.
- Toolbars collapse to two rows at most. Hide the filter entirely when there
  is nothing to filter.
- Rows stack key over value; bulk actions and row menus leave because the
  sheet has them; row-level actions move next to the title.
- Fact badges that wrap under a title stay visible.
- A side pane and the sidebar become drawers below the desktop breakpoint.
- Tab content scrolls with the page, never in a nested scroll area that
  clips under the tab divider.

## Patterns to detect and fix

1. **Ghost rows.** Placeholder or disabled rows for things that don't exist.
   List only real items.
2. **Greyed-out non-applicable items.** Scope rows that can't apply. Filter
   them out.
3. **Rank by colour.** A dimmed subtitle, value, or joining word. Restore the
   default colour and drop a size step instead.
4. **Clickable title.** A row whose only target is the name. Make the row the
   target with a hover background.
5. **Decorative chevron.** A chevron on a chip, badge, or sheet-opening row.
   Remove it; use an arrow only for navigation.
6. **Badge city.** More than three chips on a card or column. Keep the ones
   that encode state; move the rest behind an info button.
7. **Icon on every row.** Leading icons on instance rows. Move the type icon
   to the section header.
8. **Card in card.** A bordered card wrapping bordered cards. Lift the
   heading onto the page and keep the items as the surface.
9. **Doubled divider.** A border meeting another border. Keep one.
10. **Vanishing inset ring.** An inset shadow under hoverable or opaque
    children. Switch the container to a real border.
11. **Load more.** A paginated footer on a list. Replace with infinite scroll
    and virtualization.
12. **Loading sentence.** "Loading..." text, or live buttons beside a
    skeleton. Draw a skeleton of the final layout, buttons included.
13. **Empty as error.** An empty response shown in the error state. Show the
    empty state.
14. **Jumping header.** A header whose height changes between sibling views
    or states. Keep it one line and move detail into a popover.
15. **Confirm per toggle.** A dialog on every switch. Confirm once on save.
16. **Confirm the reversible.** A dialog before an undoable action. Offer
    undo in the toast instead.
17. **Per-view preference.** A display option stored per board or per
    project. Store one per-user object.
18. **Wrong home.** Project data in a create dialog or a user preference.
    Move it to the settings that own it.
19. **Copyable id row.** An id with a copy button nobody needs. Remove it.
20. **Monospace for words.** Body text or a placeholder in the mono font.
    Mono is for refs, ids, and values.
21. **Brand mark on the accent.** A logo inside an accent-coloured chip. Use
    a neutral chip and check dark mode.
22. **Faint drop target.** A translucent wash the user can't read. Make it a
    solid tint with a dashed border and a hint.
23. **Centred palette.** A command palette in the middle of the viewport.
    Anchor it under its trigger.
24. **Stacked navigation.** Tabs inside tabs, or a third tab bar. Give the
    tab a pane with sections instead. Tabs that filter one list are fine;
    the problem is two-dimensional navigation.
25. **Status badge at rest.** A "Running" badge next to a status dot. Keep
    the dot; show a word only when the state is abnormal.
26. **Missing dark twin.** A colour override with no dark-mode counterpart.
    Add it.

## Handoff check

Before you hand a screen to the user, confirm each line. The user signs off
on visuals; never self-approve.

- Every state is drawn: loading, empty, no results, error, partial.
- Rendered at the narrowest and widest supported widths, light and dark,
  with the longest real names.
- Design and code match; the board shows what the code renders.
- Each string passed the deletion pass in `writing-for-ui`.
- Project-specific conventions were checked against the project's own
  skill or agents file.

