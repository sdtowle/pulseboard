# ADR 0007: Project structure

- **Status:** Proposed (for review)
- **Date:** 2026-10-29

## Context

ADR 0006 chose a monorepo with pnpm workspaces and three packages: server, client and shared. This record sets out how those packages are laid out, where tests and configuration go, and which parts are deliberately left open.

## Decision

### Folders

```
pulseboard/
├── apps/
│   ├── server/        the API, plus the worker as a second entry point
│   └── client/        the browser dashboard
├── packages/
│   └── shared/        types and schemas used by the apps
├── docs/
└── (root config files)
```

- **`apps/`** holds things that are run or deployed. **`packages/`** holds libraries that apps use.
- Dependencies point one way: apps may depend on packages, and packages never depend on apps.
- Packages are named with a shared scope, for example `@pulseboard/shared`, so they can be imported by name.

### Server

- The server's source is organised **by layer** (for example routes, services, database and middleware). This is a layout the developer already knows, and it can change if the folders become crowded.
- Files for the same feature share a name across layers (for example `monitors.routes.ts` and `monitors.service.ts`) so a feature is easy to find.
- The API and the worker each have their own entry point, so they can run as separate processes.
- Database code lives inside the server app, behind a data-access layer. The rest of the code asks that layer for data and does not write queries or hold connections itself. It is not a separate package for now.

### Tests

- Each package has its own `tests/` folder that mirrors its source layout.
- Every package must pass its tests on its own.

### Configuration

- Tooling is configured once at the repo root: the base TypeScript settings, ESLint and Prettier. Each package extends the base and changes only what it must.
- A single `.env` file sits at the repo root, with a committed `.env.example` that lists every variable and contains no secrets. The real `.env` is ignored by git.
- The client only receives variables starting with `VITE_`. No secret may ever use that prefix.

## Rationale

- Splitting apps from packages shows at a glance what is deployed and what is reused, and the one-way rule stops circular dependencies.
- A layered server layout is familiar and quick to start with.
- Keeping database code in the server avoids a package that only one app uses, while the data-access layer keeps it easy to extract later.
- Tests per package mean each part can be checked, and later built in CI, on its own.
- One root `.env` is simpler to manage than several.

## Consequences

- Vite and the server each need to be told where to find the root `.env`. Check the current documentation for each when setting this up.
- A layered layout spreads one feature across several folders.
- If the worker or the database code needs its own dependencies or deployment, it can be moved into its own package.

## Open questions

- How the apps use the shared package (importing source or building first). This is the subject of the spike in issue #44.
- Which database to use and how the code accesses it (ticket 0.2). The decision about where the database code lives will be revisited then.

## Revisit

Review this record once the first few features are built. If the layers or the folder split cause friction, replace it with a new ADR that supersedes this one.
