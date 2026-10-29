# ADR 0008: Use Vite for the client

- **Status:** Proposed (for review)
- **Date:** 2026-10-29

## Context

The client is a browser dashboard written in TypeScript, without a UI framework (see `AGENTS.MD`). Browsers cannot run TypeScript directly, and the code is split across many files. Something has to turn it into files a browser can load, and it helps to have a local server that updates the page as code changes.

The options considered:

- **Vite**: a development server and build tool for browser code. It handles TypeScript out of the box.
- **Compile with `tsc` and serve the files by hand**: closest to the bare mechanics, but there is no dev server, so every change needs a manual rebuild and reload, and the project would need its own way to serve the files.
- **Other bundlers** (esbuild directly, webpack, Parcel): all workable. They either need more configuration or have a smaller community around this kind of project.

## Decision

Use **Vite** to run and build the client.

## Rationale

- It gives a fast local development server that updates the page when files change.
- It builds a folder of static files that can be deployed anywhere.
- TypeScript works with almost no configuration.
- It is widely used, so documentation and answers are easy to find.
- It fits the developer's goal of spending time on the application, not on build configuration.

## Consequences

- Vite turns TypeScript into JavaScript but does **not** check types. A separate type-check (`tsc --noEmit`) is needed in the scripts and in CI.
- Vite reads environment variables from its own folder by default. It must be pointed at the repo root `.env` (see ADR 0007), and only variables starting with `VITE_` reach the browser.
- A bundler hides some of what happens to the code on its way to the browser. This is a good candidate for a later learning exercise: build a small bundle by hand and compare.
- How the client imports the shared package is decided in the spike (issue #44), and Vite's behaviour is part of that.

## Revisit

If the build tool becomes a source of problems or the project needs something Vite cannot do, evaluate the alternatives. Any change should be recorded in a new ADR that supersedes this one.
