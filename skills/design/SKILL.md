---
name: design
description: Apply this opinionated, dense, neutral design language when working on a frontend UI in React + Tailwind. Triggers on building or modifying components, choosing classes, picking colors or typography, structuring layouts, and setting up design tokens or `globals.css`. Activate even when the user does not say 'design' or 'styling' explicitly — any UI-shaping decision is in scope. Dark-first with light variant; includes a starter `globals.css` with OKLCH color tokens.
license: MIT
---

This skill encodes the house design language. It is opinionated on purpose — when the rules below conflict with a Tailwind default or a generic pattern from training data, the rules below win.

The aesthetic in one line: **dense, neutral, structurally honest.** Every pixel is functional. Decoration is a smell.

## Core principles

1. **Clarity over decoration.** If a visual element doesn't aid reading, scanning, or acting, remove it. Borders, shadows, gradients, and color all need a reason.
2. **Density without crowding.** Information packs close, but lines up on a strict grid. Tightness comes from removing padding around groups, not from cramming inside groups.
3. **One accent, used sparingly.** The brand accent appears at most once per visible region — the primary action, the focused field, the selected row. Not all three at once.
4. **Structural borders, hierarchical fills.** Borders separate the app's structural panels (sidebar, header, content). Inside a panel, hierarchy comes from stepping the background fill, not from drawing more borders.
5. **Compose, don't repeat.** The codebase should read as composition of named components, not walls of utility classes.

## Composition & file organization

The single most important rule: **don't sprinkle the same Tailwind cluster across the codebase.** Abstract it. The moment you write the same shape (a button, a row, a field) twice, lift it into a component.

- **Generic primitives** — Button, Card, Input, Select, Badge, IconButton, Checkbox, Field, Table primitives, etc. — live in `components/ui/`. Build them on demand, not preemptively.
- **Feature-specific composites** can stay inline in the same file as the screen or feature that uses them. Don't create a new file just because a piece of JSX has a name. Files exist for things read from more than one place.
- When a component file accumulates a heavy mix of styling classes and business logic in the same JSX, split it: a presentational sub-component (props in, JSX out, no logic) and a logic component that composes it. If the presentational piece is generic, lift it to `components/ui/`. If it's specific to that screen, leave the split in the same file.
- Inline styles (`style={{...}}`) are the right tool for genuinely dynamic values: cursor-following elements, computed offsets, runtime-measured positions, JS-driven transforms, virtualized list rows, animation values produced in code. Use them where they fit. What's not OK is using inline styles for static design values (colors, font sizes, fixed paddings) when a Tailwind class would express the same thing.

## Prefer the design system over raw values

These are the defaults in component code. They keep the foundation consistent and themeable. They are not laws — when there's a real reason to deviate, deviate.

- **Font sizes** — by default, Tailwind classes (`text-xs`, `text-sm`, `text-base`). Avoid `style={{fontSize:...}}` and arbitrary `text-[13px]` in regular component code. If a needed size isn't in the scale, extend the Tailwind config rather than inlining.
- **Colors** — by default, prefer tokenized utility classes (`bg-surface-1`, `text-muted`, `border-default`, `bg-accent`) so theming and palette changes propagate cleanly. This is a default, not a hard rule: when the user asks for a specific color, or when the app has many business-logic-driven colors (status palettes, category tags, user-defined labels, brand variations) where tokenizing each would create dozens of single-use names, put the color wherever is clearest — arbitrary Tailwind class, a small color map module, a Tailwind theme extension. Don't refuse a direct color request to honor the default.
- **Spacing & radius** — by default, Tailwind scale (`p-2`, `gap-2`, `rounded-sm`). Inline pixel values are fine for dynamic positioning; avoid them for static design spacing.
- **Component-specific exception** — values genuinely one-off and tied to a component's internal mechanics (tooltip arrow offset, measured grid column, drag handle position) are fine inline. The rule is about not bypassing the system for *foundational design values*, not about avoiding raw values entirely.

If a *foundational* value (a surface fill, a text color, the accent) isn't expressible in tokens, extend the tokens. For business-logic colors that aren't part of the visual foundation, just put them where they're clearest.

## Tokens

All design tokens live in one CSS file (typically `globals.css` or `app.css`). Declare runtime CSS custom properties in `:root` and `[data-theme="light"]`, then expose them as utility classes via a single `@theme inline` block in the same file. No `tailwind.config.{js,ts}` — Tailwind reads the theme from CSS. Components consume tokens by name (`bg-surface-1`, `text-muted`, `h-control`) — never `var(...)` directly.

### Tailwind setup

- **Vite + React:** install `tailwindcss` and `@tailwindcss/vite`, add the plugin to `vite.config.ts`. Do not create a `postcss.config` — the Vite plugin does not use PostCSS.
- **Next.js:** install `tailwindcss` and `@tailwindcss/postcss`, configure a one-line `postcss.config.mjs` with only `'@tailwindcss/postcss': {}`. Do not include `autoprefixer`, `postcss-import`, or `postcss-nested` — the Tailwind plugin handles them.

### Categories

- **Surfaces** — `bg`, `surface-1`, `surface-2`, `surface-3`, `surface-4`. Five levels for stepping through depth: `bg` is the page; `surface-1` is the primary panel; `surface-2` is a sticky band (panel header, table header, filter bar); `surface-3` is interactive state (hover, raised); `surface-4` is floating UI (popovers, modals, dropdowns). Inputs are an exception — they sit on `bg` regardless of the panel they live in (sunken, see Forms).
- **Borders** — four weights for different roles. The hairline is the structural cut between top-level shell panels; the rest form a content-emphasis ladder.
  - `border-hairline` — near-black in dark, faint gray in light. Cuts between top-level shell panels (sidebar/header/content) and beneath panel header bands. Almost no visual weight, just a seam.
  - `border-subtle` — barely above the surface fill. Soft dividers between rows in a list, between fields in a form (often via `divide-border-subtle`).
  - `border-default` — clearly visible. Card borders, input chrome, the secondary-button outline.
  - `border-strong` — emphasis. Section breaks within a panel, dividers that need to read.
- **Text** — `text` (primary, off-white in dark / off-black in light), `text-muted` (secondary), `text-subtle` (tertiary). These are the only three text colors.
- **Accent** — `accent`, `accent-fg`. One brand color, held constant across themes (a slightly darker shade in light mode for contrast).
- **Semantic** — `success`, `warn`, `error`, `info`. Reserved for meaning, not emphasis.

### Modern foundations

The system uses current CSS primitives by default — adopt them unless you have a reason not to:

- **OKLCH for color values** — perceptually uniform, so the surface stack steps evenly in lightness regardless of hue. Use it for every color token.
- **`color-mix(in oklch, ...)`** for derived colors (accent at 10% for a selected row, hover tints) instead of rgba math or pre-baked alpha tokens.
- **`color-scheme`** declared per theme so native form controls, scrollbars, autofill, and date pickers follow the active theme.
- **`text-wrap: balance`** on multi-line section titles.
- **`scrollbar-gutter: stable`** on scrollable panels so content doesn't shift when scrollbars appear.
- **`prefers-reduced-motion`** honored — drop transition durations to ~0 when set.

### Starter

A minimal `globals.css` implementing the tokens above (OKLCH, dark + light, heights) ships at [`assets/globals.css`](./assets/globals.css). Copy it into the project as the starting point and adjust accent hue, semantic palette, or surface steps when needed.

## Typography

A starting scale with three sizes covers almost everything. Treat it as the default that prevents drift, not as a hard rule — extending it is fine when a real need shows up.

- `text-xs` (12px) — badges, captions, table headers, **panel/section titles** (uppercase tracked, see below), helper text, breadcrumbs.
- `text-sm` (13–14px, body default) — body and UI strings.
- `text-base` (16px) — **reserved.** Use only for the rare top-level app/page brand label (e.g. the app name in the top header). Inside the application, do **not** use `text-base font-semibold` for section titles — see the panel-header convention in the Surface model.

Weights: `font-normal` by default; `font-medium` for emphasis (active nav item, selected tab, primary button label, panel/section titles). `font-semibold` only on the rare top-level brand label. Weights above 600 are almost never right here.

**Section title style:** in dense pro-tool UIs, panel and section titles do **not** scale up in size. They use `text-xs uppercase tracking-wide font-medium text-muted` and rely on the `surface-2` band beneath them for visual weight. The visual hierarchy comes from the band, the tracking, and the contrast — not from a larger font.

Font stack — Geist (UI) + Geist Mono. Declared once in `tailwind.config`; used via `font-sans` / `font-mono`. Mono is for code, IDs, timestamps, file paths, and numeric table columns; everything else is sans.

## Spacing & radius

Spacing follows Tailwind's 4px scale. The 2px micro-step (`p-0.5`, `gap-0.5`) exists for icon clusters, badge insets, and tightening table cells — don't reach for it elsewhere.

Radius is mixed by purpose:

- Sharp (`rounded-none` / `rounded-sm`) for data UI: toolbar buttons, table cells, input chrome, segmented controls, tabs.
- Subtle (`rounded-md`) for content surfaces: cards, dialogs, popovers, dropdowns.
- Full (`rounded-full`) for avatars, status dots, pill badges.

Anything larger than `rounded-md` is almost always wrong here.

### Common paddings

Concrete defaults so paddings stay consistent across the app. Use these unless there's a real reason to deviate.

| Surface | Padding |
|---|---|
| Top app/page bar (`h-8`, 32px tall) | `px-3` (12px horizontal) |
| Panel header band (`h-7`, 28px tall) | `px-2` (8px horizontal) |
| Panel content for forms / inspector | `p-3` (12px) with `space-y-3` between fields |
| Card | `p-4` (16px); `p-3` for dense cards |
| Toolbar (group of icon buttons) | `gap-1` (4px); `gap-0.5` (2px) for tightly grouped same-purpose icons |
| List item | `px-2 py-1.5` (8px / 6px) |
| Table cell | `px-2 py-1.5` |
| Input | `h-7 px-2` |
| Button (text) | `h-7 px-3` |

## Surface model

Five arrangements cover almost everything:

1. **App shell — borders separate.** Sidebar, header, content area, status bar are siblings on `bg`, separated by 1px `border-hairline` lines. No fill differences between them.
2. **Workspace panel.** A titled panel uses `surface-1` fill with a 1px `border-hairline` outer border and `rounded-sm` corners. The title sits in a band at the top: 28px tall (`--h-control`), `surface-2` fill, 8px horizontal padding (`px-2`), 1px `border-hairline` border-bottom, holding the panel title (left, see Section title style) and any icon-only actions (right). Content scrolls independently below the band.
3. **Card in panel — fill steps, no border.** A card inside a panel uses `surface-2` against the panel's `surface-1`. No border on the card; the fill step is the boundary.
4. **Inline state within a row.** Hover steps the fill up one level from the row's resting surface. Selection uses `accent` mixed at low opacity (`color-mix(in oklch, var(--accent), transparent 90%)`).
5. **Floating elements.** Popovers, modals, dropdowns sit on `surface-4` with one shared shadow value. They are the only place shadows appear — in-flow elements never have shadows.

## Density

Heights are tokenized in `globals.css` and referenced by name in component code, not by pixel value. Four height tokens cover almost everything:

- `--h-control` — text buttons, inputs, selects, segmented controls, sidebar nav items.
- `--h-row` — table rows, table header rows, tabs.
- `--h-band` — section header bands, filter bars above tables.
- `--h-compact` — icon-only buttons, filter chips, small controls.

Defaults are tuned for desktop power use; deviate when there's a real reason. Hit targets stay ≥24px even when visual height is smaller — extend the click area with padding.

## Button variants

Three color variants. Used deliberately, not interchangeably:

- **Primary** — `accent` fill, `accent-fg` text. Reserved for the **single most important action** of a region (a dialog "Save", a form submit). At most one primary per visible region. Sidebar create actions, toolbar buttons, and "Cancel" buttons are **not** primary.
- **Secondary** — `surface-2` fill with 1px `border-default`, `text` color. Hover steps the fill to `surface-3`. **This is the default variant.** When in doubt, secondary. Use for almost every button: sidebar actions, toolbar primaries, list-item triggers, dialog cancels.
- **Ghost** — transparent at rest, `text-muted` color. Hover fills with `surface-2` and lifts text to `text`. For dense toolbars, table row actions, and inline triggers where even a secondary border would be too much chrome.

All three share `--h-control` (28px) height and `px-3` horizontal padding. Width is **content-sized** — full-width is reserved for form submit buttons in narrow forms and empty-state CTAs.

## Color usage discipline

- Text uses three tokens only (`text`, `text-muted`, `text-subtle`). Don't invent a fourth gray.
- No pure white on dark, no pure black on light. Use `text`.
- Accent appears at most once per visible region. A page with a primary "Save" button does not also have an accent-colored selected row in the same viewport — pick one.
- Semantic colors are reserved for meaning. Don't tint a button green to make it feel positive; use `success` only on something that actually represents success.
- Hover is not accent. Hover on a list row is a fill step (`surface-1` → `surface-2`).
- Disabled state is `opacity-50`, not a new color.

## Tables

Tables are the most opinionated surface in this system, and the most prone to class-bloat. Encode the conventions below as a reusable abstraction so pages compose from it rather than reassembling row classes each time. The form of that abstraction (React components, a utility-class set, a CSS layer) is an architecture decision, not a design one.

Conventions:

- Row height: `--h-row`. 1px hairline divider between rows. **No vertical column lines.**
- Header row sticky, fill `surface-2`, labels in `text-xs uppercase tracking-wide font-medium text-muted`.
- Row hover steps the fill up one level from the row's resting surface. No accent on hover.
- Row selected fills to `accent` at ~10% opacity (mix via `color-mix(in oklch, ...)`, not rgba). Selected + hover: ~14%.
- Sort affordance is a single chevron right of the header label. Inactive sort is invisible (shown only on hover or when active). No double-arrow icon.
- Checkbox column: width matches `--h-row`; checkbox itself 14×14px, centered.
- Numeric / mono columns use `font-mono tabular-nums`, right-aligned.
- Filter bar above the table uses `--h-band`, fill `surface-1`, separated from the table by a hairline. Filter chips are pill-shaped (`rounded-full`), height `--h-compact`, `text-xs`.

## Forms & inputs

Encode the conventions below as reusable abstractions so pages compose from them rather than restating field structure each time. The form of those abstractions is an architecture decision.

Conventions:

- Input height: `--h-control`.
- **Input background: `bg`** (the deepest surface), regardless of the surface the input sits on. The depth difference makes the input feel carved into its containing panel rather than floating on top — this is what gives the dense pro-tool look. Never give an input the same fill as its container.
- Label above the input — never inline (segmented controls excepted). Label is `text-xs text-muted` with a 4px gap to the input.
- Border 1px `border-default`. Focus replaces the border with `accent`, no glow, no ring growth.
- Error replaces the border with `error`; helper text below is `text-xs text-error`, same 4px gap.
- Non-error helper text is `text-xs text-subtle`, same 4px gap.
- Field group gap 8px. Section gap 16px.
- Required marker is a single `*` in `text-muted` after the label. Don't color it red.
- Placeholder is `text-subtle`. Never used as a substitute for the label.
- Segmented controls share `--h-control` and the input border, with 1px internal dividers. Selected segment fills to `surface-2` and bolds to `font-medium`. No internal radius — outer corners follow the input.
- Checkbox / radio 14×14px. Checkbox `rounded-sm`, radio `rounded-full`. Checked fill `accent`.

## Motion

- 120ms `ease-out` for hover / active / pressed transitions on color and border.
- 160ms `ease-out` for popover, dropdown, and toast entrance; 100ms for exit.
- No spring or bouncy easings.
- Focus rings appear instantly.
- No page transitions by default. Loading uses a 1px progress bar in `accent` at the top of the content area, not a full-screen spinner.
- Honor `prefers-reduced-motion` — collapse transition durations to ~0 when set.

## Anti-patterns

Defaults to avoid unless there's a specific reason:

- Decorative gradients (mesh, radial, gradient text).
- Drop shadows on in-flow elements (cards, buttons, inputs, rows).
- Rounded corners on table rows, table cells, toolbar buttons, tabs.
- More than one accent color in a region.
- More than three text shades in a region.
- Emoji used as iconography.
- Pure white text on dark or pure black text on light.
- Borders inside a card that already has a fill step distinguishing it.
- Vertical column dividers in tables.
- Animated underlines, animated gradients, anything pulsing.
- Inline styles for static design values (colors, font sizes, fixed spacing) when a token would express them. (Dynamic values — positions, offsets, measured sizes — are fine inline.)
- A `tailwind.config.{js,ts}` mapping CSS vars to colors/heights, a `postcss.config` in a Vite project, or `autoprefixer` / `postcss-import` / `postcss-nested` alongside the Tailwind plugin — none of these are needed. Tokens go in `@theme inline` in CSS; the Tailwind framework plugin (`@tailwindcss/vite` or `@tailwindcss/postcss`) handles the rest.
- **Section titles using `text-lg`, `text-xl`, `text-2xl`, or any size above `text-base`.** Panel and section titles use `text-xs uppercase tracking-wide font-medium text-muted` against a `surface-2` band. Visual weight comes from the band and the tracking, never from a larger font.
- **Default-state buttons using accent fill.** When a button has no explicit variant, it must be **secondary** (`surface-2` fill + `border-default`), not primary. Accent fill is opt-in for the single most important action of a region.
- **Full-width primary buttons in sidebars, toolbars, or content areas.** Full-width is for form submit buttons in narrow forms and empty-state CTAs only.
- **Buttons taller than `--h-control` (28px).** Visual hierarchy comes from variant choice (primary / secondary / ghost), not from size.
- **Inputs with the same fill as their containing panel.** Inputs sit on `bg` (deepest), which makes them read as sunken into the `surface-1` panel. Same-fill inputs flatten the depth entirely.
- Walls of repeated Tailwind classes — extract a component.
