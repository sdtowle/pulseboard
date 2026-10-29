# Contributing to PulseBoard

Welcome. This page explains how to get the project running and how changes get made, so you can pick up a ticket without asking anyone.

PulseBoard is a service health and uptime monitor. It is also a learning project, so the way it is built matters as much as what it does. Read [docs/project-brief.md](docs/project-brief.md) for the background.

## Contents

- [Before you start](#before-you-start)
- [Set up the project](#set-up-the-project)
- [How work is organised](#how-work-is-organised)
- [Making a change](#making-a-change)
- [Where decisions are written down](#where-decisions-are-written-down)
- [Working with AI](#working-with-ai)

## Before you start

You will need:

- Node.js (the version will be listed here once the project is set up)
- Git

The project is written in TypeScript and runs on Node.js. Code is checked with ESLint and formatted with Prettier.

## Set up the project

> This section is a placeholder. It will be filled in as part of [ticket 0.1](https://github.com/sdtowle/pulseboard/issues/1) once the project has code to run.

When it is ready, this will cover:

1. Installing dependencies
2. Setting environment variables (using `.env.example`)
3. Starting the app
4. Running the tests
5. Running the linter and formatter

## How work is organised

All work is tracked as GitHub issues. Each issue is a small user story with a checklist of acceptance criteria. A ticket is done when every box is ticked.

The full list lives in [docs/backlog.md](docs/backlog.md). Each issue carries a few labels:

| Label | Meaning |
|---|---|
| `epic:0` to `epic:4` | The theme the ticket belongs to |
| `must`, `should`, `could`, `wont` | How important it is (MoSCoW) |
| `size:S`, `size:M`, `size:L` | Roughly how long it takes |
| `adr` | The ticket is about writing a decision record |
| `spike` | A short experiment to learn something |

If a word is unfamiliar, check the [glossary](docs/glossary.md). It is meant to be added to whenever something trips you up.

**A feature counts as done when it is working, tested, merged to `main` and deployed.**

## Making a change

One ticket at a time. Every change starts from a ticket and goes through a branch. Nothing is committed straight to `main`.

### 1. Create a branch

Name it `type/issue-number-short-description`:

| Type | Use it for |
|---|---|
| `feature/` | New functionality |
| `bugfix/` | Fixing something broken |
| `chore/` | Tooling, config, dependencies |
| `docs/` | Documentation only |

Examples:

```
feature/12-http-server
docs/2-database-adr
chore/1-eslint-prettier
```

Use the GitHub issue number, and keep the description to two or three words.

### 2. Make small commits

Write commit messages as plain sentences that say what changed.

```
Add the monitor schema
Handle timeouts in the HTTP check
```

### 3. Check your work before you push

Run the tests, the linter and the formatter. The exact commands will be listed in [Set up the project](#set-up-the-project) once they exist.

### 4. Open a pull request

Open a pull request into `main`. In the description, explain what changed and why, and add `Closes #12` (using your ticket number) so the ticket closes itself when the pull request is merged.

Keep pull requests small, with one ticket each.

### 5. Merge with squash

Pull requests are merged with **squash**. All the commits on your branch are combined into a single commit on `main`, using the pull request title as its message. That means the history of `main` reads as one line per ticket.

Delete the branch after merging.

### About `main`

`main` is the source of truth and should always work.

For now, the rules above are a habit that is not enforced. Once the CI pipeline is in place ([ticket X.4](https://github.com/sdtowle/pulseboard/issues/39)), `main` will be protected: changes must come through a pull request, the tests must pass, and the rules will apply to the repository owner too.

## Where decisions are written down

Significant technical decisions are recorded as short documents in [docs/adr](docs/adr). Each one explains the problem, the options that were considered, what was chosen, and what that choice costs.

Read these if you want to know why something is built the way it is. If you make a decision that is hard to reverse, add one. Number it next in the sequence and never edit an old decision to change its meaning. Write a new one that replaces it instead.

## Working with AI

This is a learning project, so the code is written by hand. AI is used as a mentor and reviewer: it asks questions, points out edge cases and checks standards. It does not write the application code.

The one exception is the initial project setup, such as folder structure, dependency lists and configuration. Documentation can also be drafted by AI on request and is then edited by the developer.

The full rules are in [AGENTS.MD](AGENTS.MD).
