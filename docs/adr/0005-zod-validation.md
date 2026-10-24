# ADR 0005: Use Zod for runtime validation

- **Status:** Accepted
- **Date:** 2026-10-24

## Context

TypeScript types are erased at compile time (ADR 0002), so they cannot protect against bad data from outside the application: HTTP request bodies, query parameters, environment variables, database rows and third-party APIs. Express (ADR 0004) provides no validation.

Alternatives considered:

- **Valibot**: smaller bundle size, similar schema approach.
- **ArkType**: TypeScript-syntax-style schemas, newer.
- **Yup**: older, with weaker TypeScript inference.
- **Fastify-style JSON Schema**: not applicable with Express without extra tooling.

## Decision

Use **Zod** to define schemas, validate external input at runtime, and infer TypeScript types from those schemas.

## Rationale

- One schema is the single source of truth for both validation and the static type (`z.infer`), so they cannot drift apart.
- It is the most widely used option for TypeScript, with the most documentation and examples.
- Schemas can be shared between front end and back end to keep the API contract consistent.
- It teaches validating at the system boundary.

## Consequences

- Add validation middleware (or helpers) that parse `req.body`, `req.query` and `req.params` against a schema and return a consistent error response on failure.
- Validate environment variables at startup so the app fails early on bad configuration.
- Prefer `safeParse` or a central error handler so validation errors map to 400 responses rather than unhandled exceptions.
- Schema definitions become part of the API contract and should be kept in a clearly organised location.
- Adds a runtime dependency and a small amount of validation overhead, which is acceptable here.

## Revisit

If bundle size or performance matters on the front end, evaluate Valibot. Any change should be recorded in a new ADR that supersedes this one.
