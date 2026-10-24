# ADR 0001: Use Node.js as the runtime

- **Status:** Accepted
- **Date:** 2026-10-24

## Context

Pulseboard is a learning project. The goal is to learn architectural principles and build a full-stack application end to end, not to learn a new language.

Two runtimes were considered:

- **Node.js**: a language and ecosystem already familiar to the author, and one language across front end and back end.
- **Go**: approachable syntax, low-level control, strong concurrency model and performance.

## Decision

Use **Node.js** as the runtime for the application.

## Rationale

- Learning a new language alongside full-stack principles would add overhead and distract from the project's actual goal.
- Using JavaScript/TypeScript across the stack reduces context switching.
- Go's advantages (threading, low-level management, raw performance) are not needed at this stage.

## Consequences

- Faster iteration, with the learning effort focused on architecture and full-stack design.
- Weaker CPU-bound and multi-threaded performance than Go. Acceptable for current needs.

## Revisit

Once the app is in a stable position, re-evaluate by prototyping or porting selected parts in another language (e.g. Go) and comparing the results. Any change of runtime should be recorded as a new ADR that supersedes this one.
