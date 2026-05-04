---
name: clean-web-development
description: Use when creating, refactoring, reviewing, or extending TypeScript React web apps — for stack selection, project scaffolding, folder and file organization, imports, dependency choices, code style, UI primitive structure, and picking between Next.js (SSR/content), Vite (interactive client-heavy), or Vite + Convex (realtime/reactive data). Activate even when the user does not say 'architecture' or 'clean code' explicitly — any structural decision in a TypeScript React codebase is in scope.
license: MIT
---

# Clean Web Development

## Purpose

Write web applications in a minimal, clean, human-readable style.

The goal is not only to make the application work. The goal is to create a codebase that feels carefully written, easy to continue, easy to navigate, and ready for open-source-level review.

A human developer should be able to open the project, understand the structure quickly, and keep building without fighting the architecture. The code should feel like it was written by someone who cares about taste, clarity, consistency, and long-term maintainability.

## Core Philosophy

Prefer less code, but never at the cost of clarity.

Every file, function, component, dependency, folder, abstraction, and pattern should earn its place. Readable code is more important than clever code. Avoid unnecessary abstractions, but also avoid large messy blocks of logic.

The codebase should feel calm, direct, and intentional. Someone reading it should be able to understand what is happening without mentally untangling huge components, vague helpers, hidden side effects, or unnecessary indirection.

Good code should feel pleasant to read. It should be obvious where things live, what each function does, why each dependency exists, and how a human should continue the work.

Working code is the minimum requirement. Clean, minimal, maintainable code is the actual standard.

## General Coding Style

User requirements and existing project conventions override these defaults. Apply the style without creating unnecessary churn.

For new TypeScript web projects, prefer TypeScript, React, Bun, Tailwind CSS, Biome, and Vitest when they fit. Use Bun as the package manager, and use Bun as the runtime when practical. Do not migrate an existing project to Bun, Biome, Tailwind, or another preferred tool unless the user asks for that migration or the change is clearly justified.

Use the latest stable versions for new projects. Before using a framework or library API in a non-trivial way, consult current official documentation when tool access allows. Follow the currently recommended patterns instead of legacy APIs or outdated examples.

Code, comments, examples, file names, and project language should be in English. Prefer names that make comments unnecessary. Add comments only when they explain non-obvious intent, constraints, or tradeoffs.

Keep functions small and focused. A function should usually do one thing. If a function becomes hard to read, grows too long, or mixes responsibilities, extract the secondary concern into another function.

Keep components focused. Avoid components with too much state, too much logic, too many handlers, or complex rendering branches. Extract sections, hooks, helpers, or child components when it improves readability.

Avoid large blocks in general: large effects, memoized calculations, event handlers, render branches, helper functions, or component returns should be questioned. If a block can be named and extracted in a way that improves readability, do it.

It is acceptable to keep multiple small components or helper functions in the same file when they are only used there. Prefer that over one huge component with a long, hard-to-read body.

When a file contains multiple local helpers or components, order them from lower-level to higher-level. Small helpers and private child components should usually appear first. The main exported component or function that gives the file its name should usually appear near the end.

Use descriptive names. A name should reduce how much code someone needs to read.

Use `kebab-case` for files and folders.

Avoid vague names when the scope is broad, such as `component.tsx`, `manager.ts`, `handler.ts`, `misc.ts`, or `stuff.ts`.

Names like `utils.ts`, `helpers.ts`, `handlers.ts`, or `store.ts` are acceptable when they live inside a small, clear scope. They become a problem only when the scope is broad, unclear, or growing. A small feature can have a `utils.ts`; a large or mixed one should split that file into more specific modules.

## Imports and Aliases

Configure the project to use the `@/` alias for root-level imports.

Use `@/` imports for code that crosses features, reaches shared root folders, or is clearer when imported from the project root. Relative imports are fine for nearby files, especially inside the same feature or folder.

Keep imports grouped from general to specific: external libraries first, app-level `@/` imports second, nearby relative imports last. Do not be overly rigid about alphabetical ordering.

## Folder Structure Philosophy

The folder structure should be easy for a human to navigate.

Do not create folders just because a template usually has them. Create folders when they reduce cognitive load.

Avoid the `src/` wrapper folder. Place code folders (`app`, `components`, `features`, `hooks`, `lib`, etc.) directly at the project root, alongside the config files. The root stays clean through folder organization and naming, not by nesting everything one level deeper. This applies to all TypeScript React setups.

If a folder would contain only one or two files, it often does not need to exist yet. If a folder grows beyond roughly 10–15 files, consider splitting it. Start simple, then introduce structure as the project grows.

Keep generic shared code separate from domain-specific code.

Root-level shared folders such as `components`, `hooks`, and `lib` should contain generic reusable code. Domain-specific code should move into `features` once the project has enough size, many components, or clear product areas.

A feature is a self-contained product module. It owns the components, hooks, helpers, state, and logic that belong to that part of the product. Do not create fake feature folders: features should reflect real product domains or areas.

A feature can be small and simple, with only an `index.ts` plus a few local files. It can also have its own internal `components`, `hooks`, `lib`, or other folders when it grows. Inside a feature, the same general rules apply: stay simple when there are few files, split folders when the module grows, and keep the structure easy to scan.

Each feature should expose its public API through `index.ts`. Treat `index.ts` as an intentional boundary, not as a place to blindly re-export everything. When another feature or app-level file needs to use a feature, import from the feature entrypoint instead of reaching into private internals.

Features may depend on each other when the domain relationship is natural, but unnecessary coupling and circular dependencies should be avoided. The app layer should compose features; it should not become a place where all product logic accumulates.

## Dependency Philosophy

Use trusted libraries when they remove meaningful complexity.

Do not install a dependency for something that would be clearer as a small local function. Do install a dependency when it avoids fragile custom code, solves a real problem well, or saves a significant amount of implementation and maintenance.

Prefer boring, popular, maintained tools. Avoid obscure dependencies unless there is a strong reason.

## UI Components

Use Tailwind CSS by default. Tailwind configures via `@theme` in CSS — no `tailwind.config.{js,ts}`. With Vite, install `tailwindcss` and `@tailwindcss/vite`, add the plugin to `vite.config.ts`, and do not create a `postcss.config`. With Next.js, install `tailwindcss` and `@tailwindcss/postcss`, configure a one-line `postcss.config.mjs` with only `'@tailwindcss/postcss': {}`, and do not add `autoprefixer`, `postcss-import`, or `postcss-nested` — the Tailwind plugin handles them.

Prefer building local UI primitives in `components/ui` instead of immediately installing a component library.

Use `shadcn/ui` only when the user asks for it or when the project clearly benefits from several accessible UI primitives. Do not add it by default for simple projects.

The preferred pattern is often similar to `shadcn/ui` structurally: local UI components that are easy to inspect and modify. But avoid unnecessary Radix UI dependencies when a custom component would be simpler.

UI implementation should still be complete enough to feel intentional. Add loading, empty, error, disabled, and accessible states when the product flow requires them, but keep visual design decisions outside the scope of this skill.

## Project Type Decision Guide

Classify the project before choosing the architecture. If the user already specified a stack, respect it and apply this style within that stack.

Most TypeScript web projects should fall into one of three main types.

### 1. Content and Navigation Apps

Use this type when the application benefits from server-side rendering, URL-based navigation, public pages, SEO, or page-oriented browsing.

The key idea is that the product behaves like a navigable website. Pages matter. URLs matter. Server-rendered content matters. The user experience is largely built around moving between pages or consuming content that can be rendered before client-side interaction starts.

Use Next.js with App Router for this case.

Keep `app` focused on routing, layouts, pages, and composition. Do not let route files become large containers of business logic.

For simple projects, keep the structure simple:

```txt
app/
components/
  ui/
hooks/
lib/
```

For larger projects with many components or clear product areas, add `features`:

```txt
app/
components/
  ui/
features/
hooks/
lib/
```

In Next.js, prefer Server Components where they naturally fit. Use Client Components only when interactivity, browser APIs, state, effects, or event handlers require them. Keep `use client` as low in the tree as practical.

### 2. Interactive Product Apps

Use this type when the application behaves more like a tool than a website.

The key idea is that the product’s complexity lives mostly in the browser. The user is manipulating state, editing content, working inside an authenticated area, or interacting with a rich interface. The network should usually move data, not fully rendered HTML.

Use Vite for this case.

For Vite apps, place code folders (`app`, `components`, `features`, `hooks`, `lib`) directly at the project root — do not use the default `src/` wrapper. The Vite entry (`main.tsx`) lives at the root and is referenced from `index.html` as `/main.tsx`. Apply the same simple-to-feature-based folder progression. Keep `main.tsx` and `app.tsx` focused on bootstrapping and high-level composition.

Add a router when the app has real navigation. Prefer React Router or TanStack Router instead of manual route conditionals in one large file.

Do not create a backend unless the project needs one. When it does, choose the smallest reliable option. Use Express for more complex backends, especially when WebSockets, middleware, or flexible server behavior matter. Use Hono or `Bun.serve` for simpler APIs when they fit better.

When querying a backend and Convex is not used, use TanStack Query by default for server state. Do not rebuild cache invalidation, retries, loading states, and stale data behavior manually unless the app is extremely small. Use local React state first for local UI state. Use Zustand when shared client-side state becomes real enough to justify a small store. Do not put server cache into Zustand.

### 3. Realtime and Data-Reactive Apps

Use this type when Convex is a natural fit.

Convex is a realtime database and backend platform. It should be the central architectural choice for this case, not just another dependency.

The key idea is that shared remote data should feel live. Multiple clients may need to react to the same state, and the app would otherwise require manual synchronization, polling, cache invalidation, or custom realtime infrastructure.

This is not just “an app that has realtime somewhere.” This is an app where reactive data, shared state, or multiuser behavior is important enough that Convex simplifies the product architecture. Realtime can be the core of the product or a meaningful advantage, but do not use Convex when a simpler backend or static data flow would be clearer.

Prefer Convex functions over creating a separate Express or Hono backend. A separate backend should be the exception, used only when there is a clear reason such as custom endpoints, special integrations, unusual performance constraints, or protocols that do not fit Convex well.

A Convex project should usually be a SPA with a `convex` folder at the root. The rest of the frontend structure should follow the same simple-to-feature-based progression described above.

Convex is excellent for realtime application data. For very high-frequency collaboration, such as live cursors, character-level editing, whiteboards, canvas collaboration, or CRDT-style document editing, consider a specialized collaboration layer. Convex can still remain the main database and backend-function layer.

When using Convex, read the current Convex documentation and follow its recommended project structure, generated API patterns, and React integration patterns. If a dedicated Convex skill or project-specific Convex guidance is available, use it for implementation details.

### 4. Other Projects

If the project does not clearly fit one of the three main types, choose the simplest architecture that satisfies the requirements.

Do not force Next.js, Vite, Express, Hono, or Convex into a project where they do not fit.

For unusual projects, keep the stack minimal, use TypeScript and Bun when practical, avoid unnecessary frameworks, and make the structure easy to navigate.

## Operating Rules for Agents

Before writing code, classify the project:

1. Use Next.js App Router when the app benefits from SSR, navigation, public pages, or SEO.
2. Use Vite when the app is a client-heavy interactive product.
3. Use Vite plus Convex when reactive shared data is central enough to simplify the architecture.
4. Use the simplest fitting architecture for everything else.

While writing code:

- prefer less code;
- keep files small;
- keep functions small;
- keep components focused;
- use `kebab-case`;
- configure and use the `@/` alias;
- use relative imports for nearby files;
- group imports from general to specific;
- order local declarations from lower-level to higher-level;
- avoid unnecessary folders;
- split folders that grow too large;
- avoid manual routing hacks;
- avoid unnecessary dependencies;
- use trusted dependencies when they remove real complexity;
- check current official docs before using a library in a non-trivial way;
- use current recommended APIs and patterns;
- avoid technical debt;
- avoid placeholders unless the user explicitly asks for a rough prototype;
- avoid large blocks of logic;
- avoid cleverness;
- keep the codebase easy for a human to continue.

Before finishing, review the codebase as a whole. Remove unused code, unused dependencies, dead abstractions, vague names, oversized files, and logic that ended up in the wrong layer. If uncertain, choose the simpler option and briefly explain the tradeoff.

The final result should feel like it was written by a careful human developer who values simplicity, taste, maintainability, and long-term readability.
