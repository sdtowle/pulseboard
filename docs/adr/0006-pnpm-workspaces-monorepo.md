# ADR 0006: Use a monorepo with pnpm workspaces

- **Status:** Proposed (for review)
- **Date:** 2026-10-29

## Context

PulseBoard has three kinds of code: a server (the REST API and real-time updates), a background worker (the scheduler and check runner), and a browser client (the dashboard). They need to share some code, such as data types and validation schemas (see ADR 0005).

The project needs one clear way to organise this code into packages.

The options considered:

- **A. One package.** A single `package.json`, with folders for each part. It is the simplest setup, but client and server dependencies mix together, and nothing stops server code leaking into the client.
- **B. Several packages without workspaces.** Each folder has its own `package.json`, and a root script runs them together. This is how an earlier attempt at the project was set up. It keeps boundaries clear, but tooling gets duplicated, installs are separate, and sharing code is awkward.
- **C. Several packages with workspaces (a monorepo).** The same layout as B, but the root declares the folders as workspaces. One install covers everything, and packages can depend on each other by name.
- **D. Workspaces plus a build orchestrator** such as Turborepo or Nx. It helps when builds are slow or there are many packages, which is not the case here.

For the package manager, npm, pnpm and Yarn all support workspaces.

## Decision

Use **a monorepo with pnpm workspaces** (option C).

The repository will contain these packages:

- **server:** the API. The worker lives here too, as a second entry point, so it can run as its own process.
- **client:** the browser dashboard.
- **shared:** types, validation schemas and constants used by the other two.

Tooling lives once at the root: the base TypeScript configuration, ESLint, Prettier, and scripts that run across all packages. Each package extends the root configuration and overrides it only where it has to. The client targets a browser and the others target Node.

## Rationale

- Sharing schemas and types between the client and the server is simple. This is the main reason for choosing workspaces over separate packages.
- One install, one lockfile and one set of tooling keep the setup consistent.
- It is the structure used in many professional codebases, which fits the learning goals of the project.
- The initial setup is more work than the alternatives, but learning that setup is part of the point.
- pnpm was chosen to learn it. It is fast and strict about dependencies: a package can only import what it has declared, which catches mistakes that other package managers hide.

## Consequences

- There are more concepts to learn, such as workspace linking, hoisting and how packages resolve each other.
- The worker is part of the server package but runs as a separate process. This follows the job queue work in the backlog (ticket 3.7), where workers must run separately from the API. If the worker grows enough to need its own dependencies or deployment, it can be moved into its own package.
- pnpm needs to be installed alongside Node. Contributors can use Node's built-in `corepack` for this.
- How the other packages use `shared` is not decided yet. It could import the source directly or use a compiled version. This will be decided by a spike (issue #44) and recorded in its own ADR.
- Folder names (for example `packages/` versus `apps/`) are decided when the project structure is set up.

## Revisit

If the project grows to many packages or builds become slow, consider adding a build orchestrator (option D). If the structure turns out to be too heavy, it can be simplified to a single package. Any change should be recorded in a new ADR that supersedes this one.
