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

All design tokens live in one CSS file (typically `globals.css` or `app.css`). **Default to Tailwind v4** — declare runtime CSS custom properties in `:root` and `[data-theme="light"]`, then expose them as utility classes via a single `@theme inline` block in the same file. No `tailwind.config.{js,ts}` is needed; v4 reads the theme from CSS. Components consume tokens by name (`bg-surface-1`, `text-muted`, `h-control`) — never `var(...)` directly.

### Tailwind setup

- **Vite + React:** install `tailwindcss` and `@tailwindcss/vite`, add the plugin to `vite.config.ts`. **Do not create `postcss.config.{js,ts,mjs}`** — v4 with the Vite plugin doesn't use PostCSS.
- **Next.js:** install `tailwindcss` and `@tailwindcss/postcss`, configure a one-line `postcss.config.mjs` containing only `'@tailwindcss/postcss': {}`. **Do not include `autoprefixer`, `postcss-import`, `tailwindcss/nesting`, or `postcss-nested`** — the v4 plugin handles all of that.
- **No `tailwind.config.{js,ts}`** — if a Tailwind v3 codebase still pins `tailwind.config`, mirror the same mappings in `theme.extend`. For everything new, prefer v4.

### Categories

- **Surfaces** — `bg`, `surface-1`, `surface-2`, `surface-3`, `surface-4`. Five levels for stepping through depth: `bg` is the page; `surface-1` is the primary panel; `surface-2` is a sticky band (table header, filter bar, sub-panel); `surface-3` is interactive state (hover, raised input chrome); `surface-4` is floating UI (popovers, modals, dropdowns). Five is intentional — the system targets dense desktop UIs that need more depth gradations than typical web apps.
- **Borders** — `border` (hairline default), `border-strong` (dividers needing weight).
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

- `text-xs` (12px) — badges, captions, table headers, helper text, breadcrumbs.
- `text-sm` (13–14px, body default) — body and UI strings.
- `text-base` (16px) — section titles.

Weights: `font-normal` by default; `font-medium` for emphasis (active nav item, selected tab, primary button label); `font-semibold` reserved for section titles. Weights above 600 are almost never right here.

Font stack — Geist (UI) + Geist Mono. Declared once in `tailwind.config`; used via `font-sans` / `font-mono`. Mono is for code, IDs, timestamps, file paths, and numeric table columns; everything else is sans.

## Spacing & radius

Spacing follows Tailwind's 4px scale. The 2px micro-step (`p-0.5`, `gap-0.5`) exists for icon clusters, badge insets, and tightening table cells — don't reach for it elsewhere.

Radius is mixed by purpose:

- Sharp (`rounded-none` / `rounded-sm`) for data UI: toolbar buttons, table cells, input chrome, segmented controls, tabs.
- Subtle (`rounded-md`) for content surfaces: cards, dialogs, popovers, dropdowns.
- Full (`rounded-full`) for avatars, status dots, pill badges.

Anything larger than `rounded-md` is almost always wrong here.

## Surface model

Four arrangements cover almost everything:

1. **App shell — borders separate.** Sidebar, header, content area, status bar are siblings on `bg`, separated by 1px hairlines. No fill differences between them.
2. **Card in panel — fill steps, no border.** A card inside a panel uses `surface-1` against the panel's `bg` (or steps from `surface-1` to `surface-2` if the panel is itself raised). No border on the card; the fill step is the boundary.
3. **Inline state within a row.** Hover steps the fill up one level from the row's resting surface. Selection uses `accent` mixed at low opacity (`color-mix(in oklch, var(--accent), transparent 90%)`).
4. **Floating elements.** Popovers, modals, dropdowns sit on `surface-4` with one shared shadow value. They are the only place shadows appear — in-flow elements never have shadows.

## Density

Heights are tokenized in `globals.css` and referenced by name in component code, not by pixel value. Four height tokens cover almost everything:

- `--h-control` — text buttons, inputs, selects, segmented controls, sidebar nav items.
- `--h-row` — table rows, table header rows, tabs.
- `--h-band` — section header bands, filter bars above tables.
- `--h-compact` — icon-only buttons, filter chips, small controls.

Defaults are tuned for desktop power use; deviate when there's a real reason. Hit targets stay ≥24px even when visual height is smaller — extend the click area with padding.

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
- Label above the input — never inline (segmented controls excepted). Label is `text-xs text-muted` with a 4px gap to the input.
- Border 1px `border`. Focus replaces the border with `accent`, no glow, no ring growth.
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
- **Tailwind v3 leftovers in a v4 project:** a separate `tailwind.config.{js,ts}` mapping CSS vars to colors/heights/etc., a `postcss.config.{js,ts,mjs}` in a Vite project, or `autoprefixer` / `postcss-import` / `postcss-nested` listed alongside the v4 plugin. v4 replaces all of that with `@theme inline` in CSS plus the appropriate framework plugin (`@tailwindcss/vite` for Vite, `@tailwindcss/postcss` for Next.js).
- Walls of repeated Tailwind classes — extract a component.
