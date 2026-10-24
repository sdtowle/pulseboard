# ADR 0002: Use TypeScript as the language

- **Status:** Accepted
- **Date:** 2026-10-24

## Context

ADR 0001 chose Node.js as the runtime. On Node, the language choice is JavaScript or TypeScript.

Pulseboard is a learning project focused on architectural principles and full-stack development. Go was considered as the runtime partly because its static types force clarity. TypeScript offers much of that benefit without leaving the Node ecosystem.

## Decision

Use **TypeScript** for all application code, front end and back end.

## Rationale

- Static types make interfaces, data shapes and API contracts explicit, which supports learning good design.
- Compiler feedback catches a class of errors early and makes refactoring safer.
- Types can be shared between front end and back end, keeping contracts consistent.
- It is a small step from JavaScript, so it adds little learning overhead.
- Clear, typed boundaries make it cheaper to replace a component with another language later (see ADR 0001, Revisit).

## Consequences

- A build/typecheck step and a `tsconfig.json` are needed.
- Strict mode should be enabled from the start (`"strict": true`).
- Types are erased at runtime, so external input (HTTP bodies, env vars, database rows) still needs runtime validation at the boundaries.
- Slightly more setup and tooling than plain JavaScript.
