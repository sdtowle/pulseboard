# ADR 0003: Use ESLint and Prettier for linting and formatting

- **Status:** Accepted
- **Date:** 2026-10-24

## Context

ADR 0001 and ADR 0002 chose Node.js and TypeScript. The project needs consistent code style and automated checks for common mistakes.

ESLint is a linter, not a formatter; its own formatting rules are deprecated. Formatting is normally handled by a separate tool.

Alternatives considered:

- **Biome**: a single fast tool that lints and formats. Smaller rule set and more limited type-aware linting.
- **Oxlint (+ Oxfmt or Prettier)**: a very fast Rust linter. Newer, with fewer plugins.
- **ESLint with `@stylistic`**: one tool, but a less common approach to formatting.
- **dprint**: a fast formatter only, with a smaller community than Prettier.

## Decision

Use **ESLint** (with `typescript-eslint`) for linting and **Prettier** for formatting.

## Rationale

- This is the most widely used setup, so documentation, examples and community help are plentiful.
- `typescript-eslint` provides type-aware rules (e.g. `no-floating-promises`) that help when learning async code in Node.
- Separating linting from formatting keeps each tool's job clear.

## Consequences

- Two tools to configure; `eslint-config-prettier` is needed so ESLint does not conflict with Prettier's formatting.
- Slower runs than Rust-based tools. Acceptable at this project's size.
- Add `lint` and `format:check` scripts so local runs and CI behave the same.
- Consider a pre-commit hook (Husky with lint-staged) once the project has more files.

## Revisit

If tooling speed or configuration overhead becomes a problem, evaluate Biome or Oxlint. Replacing either tool should be recorded in a new ADR that supersedes this one.
