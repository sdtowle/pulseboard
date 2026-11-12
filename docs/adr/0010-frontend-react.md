# ADR 0010: Front end with React, a Vite single-page app and TanStack Router

- **Status:** Proposed (for review)
- **Date:** 2026-11-12

## Context

ADR 0008 chose Vite to build and serve the client but left open whether it uses a UI framework. `AGENTS.MD` originally assumed none (vanilla DOM and a hand-written reactive store).

The developer is an experienced front-end developer who knows React and Preact, and has used Next.js and Remix a little. The main learning goals of the project are in the back end, the database and software architecture, so the front end should not add too much that is new at the same time.

The back end is a separate Express API (ADR 0004, ADR 0006), so the client has no need for server-side rendering.

The options considered:

- **No framework (vanilla TypeScript):** teaches how state and DOM updates work, but is slow to build, and state management is a large piece of work on its own.
- **React:** familiar, with the largest ecosystem and the most documentation.
- **Preact:** a smaller library with a very similar API. Less common in job listings.
- **Svelte:** appealing to learn, but a new framework on top of everything else.
- **A meta-framework** (Next.js, TanStack Start, React Router framework mode): adds its own server and server rendering, which overlaps with the Express API.

For routing, **TanStack Router** and **React Router** were compared. Both work in a single-page app.

## Decision

- Build the client as a **React single-page application**, built and served by **Vite** (ADR 0008).
- Use **TanStack Router** for routing, mainly for type-safe URLs and links that fit the project's strict TypeScript approach.
- Do **not** use a meta-framework or server-side rendering.
- **Fetch data by hand first.** The first version of the monitor list (ticket 2.7) loads data with `fetch` and React's built-in hooks, with loading and error states written by the developer. TanStack Query is considered only afterwards (see below).

## Rationale

- React is familiar, which keeps the new learning in the back end, database and infrastructure.
- A single-page app fits the separate Express API and avoids a second server.
- TanStack Router's type safety extends the strict TypeScript and shared-schema approach to URLs.
- Writing data fetching by hand first means the problems that libraries solve (loading and error states, duplicate requests, stale data, out-of-order responses) are felt before a library hides them.

## Consequences

- `AGENTS.MD`, the project brief, ADR 0008 and the backlog's front-end tickets have to match this decision.
- **TanStack Query is not decided.** After the hand-written version of ticket 2.7, compare the two and record the result, either as a new ADR or a note in this one. It would also be the natural place for live updates from SSE to update cached data.
- The "framework-free" principle in `AGENTS.MD` is replaced with the learning approach "build with established tools, then rebuild selected pieces by hand as exercises". Rebuilding the Proxy-based store (ticket 2.9) or a minimal router could be such exercises, and whether they stay in the backlog is for the developer to decide.
- TanStack Router is newer than React Router, with fewer tutorials. If it causes problems, React Router remains a straightforward alternative.

## Not yet decided

- Styling (plain CSS, Sass, Tailwind or something else)
- How charts are drawn (the backlog says no chart library for ticket 2.10)
- Form handling and client-side state beyond what React provides
- How the client uses the shared package (the spike, issue #44)

## Revisit

After the hand-written data fetching exercise, and after the first few screens are built. Any change should be recorded in a new ADR that supersedes this one.
