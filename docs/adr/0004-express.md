# ADR 0004: Use Express as the web framework

- **Status:** Accepted
- **Date:** 2026-10-24

## Context

The back end runs on Node.js with TypeScript (ADR 0001, ADR 0002). It needs an HTTP framework. The front end has not been decided, so the back end is built as a standalone JSON API that any front end can consume.

Alternatives considered:

- **Fastify**: schema-first validation and good TypeScript support. Adds its own concepts (plugins, encapsulation, hooks) to learn.
- **Hono**: small, web-standards-based, with typed routes. Newer, with a smaller community.
- **NestJS**: opinionated, decorator and dependency-injection based. Adds significant abstraction on top of learning architecture.
- **Koa**: minimal, but offers little over the options above.

## Decision

Use **Express** as the web framework, built as a separate API independent of the front end.

## Rationale

- The project's goal is to learn principles, and Express keeps the HTTP layer visible: routes, middleware, request and response.
- It has the largest ecosystem, the most documentation and the most community answers.
- It provides no built-in validation, error handling or structure, so those must be built deliberately, which is part of the learning goal.
- Its middleware model is common across many frameworks and real codebases.
- Performance differences from Fastify are not relevant at this project's scale.

## Consequences

- Typing handlers needs `@types/express` and takes more effort than in a TypeScript-first framework.
- Request validation is not included. It is handled separately (see ADR 0005).
- Error handling, project structure and conventions must be defined by the project.
- Use a current Express version with proper async error handling (Express 5); confirm the version at install time.
- Keeping the API boundary clean leaves the option of replacing the framework or back end later.

## Revisit

If validation, typing or structure overhead becomes a burden, evaluate Fastify or Hono. Any change should be recorded in a new ADR that supersedes this one.
