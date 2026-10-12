# Doc outlines

Outlines, not forms. Drop any section the repo gives you nothing for, and add one where
the project needs it. Replace every `<...>` with real content or a `TODO:` marker.

- [AGENTS.md](#agentsmd) · [CLAUDE.md](#claudemd) · [README.md](#readmemd) · [HANDOFF.md](#handoffmd)
- [TASKS.md](#tasksmd) · [DECISIONS.md](#decisionsmd) · [KNOWN_ISSUES.md](#known_issuesmd)
- [docs/ARCHITECTURE.md](#docsarchitecturemd) · [docs/TESTING.md](#docstestingmd)
- [docs/FEATURES.md](#docsfeaturesmd) · [docs/API_REFERENCE.md](#docsapi_referencemd) · [docs/DATABASE_SCHEMA.md](#docsdatabase_schemamd) · [docs/DATA_FLOW.md](#docsdata_flowmd)
- [docs/SECURITY.md](#docssecuritymd) · [docs/DEPLOYMENT.md](#docsdeploymentmd) · [docs/TECH_DEBT.md](#docstech_debtmd)
- [CHANGELOG.md](#changelogmd) · [CONTRIBUTING.md](#contributingmd) · [Nested AGENTS.md](#nested-agentsmd)

---

## AGENTS.md

```markdown
# <Project name>

<One sentence: what this is and who uses it.>

## Commands
- Install: `<cmd>`
- Run: `<cmd>`
- Test: `<cmd>` (single test: `<cmd>`)
- Lint / format: `<cmd>`
- Build: `<cmd>`

## Layout
- `<path>/` - <what lives here>

## Conventions
- <only the ones a newcomer would get wrong: naming, error handling, patterns in use>

## Gotchas
- <things that look wrong but are intentional, slow or flaky steps, required env>

## Working agreement
- Read `HANDOFF.md` before starting and update it before stopping.
- Record significant choices in `DECISIONS.md` and failed attempts in `KNOWN_ISSUES.md`.

## More
- [Architecture](docs/ARCHITECTURE.md)
- [Testing](docs/TESTING.md)
```

List under "Working agreement" and "More" only the files that exist in the chosen tier.

## CLAUDE.md

```markdown
@AGENTS.md
```

Nothing else, unless there is an instruction that applies to Claude Code only.

## README.md

```markdown
# <Project name>

<What it does, in two or three sentences.>

## Quick start
<Prerequisites, install, run - the shortest path to seeing it work.>

## Usage
<The main ways to use it, with one real example.>

## Configuration
<Environment variable names and what each controls. Names only, never values.>

## Tests
<One command, then a link to docs/TESTING.md if it exists.>

## Project structure
<Top-level folders, one line each.>

## License
<Only if a licence file exists.>
```

For an empty repo, write the title, the description from the user's answer, the intended
stack, and a "Status: not started" line. Leave the rest out until there is code.

## HANDOFF.md

```markdown
# Handoff

_Last updated: <YYYY-MM-DD>_

## Current state
<What works right now.>

## In progress
<What was being changed, and how far it got.>

## Next action
<The single next step, specific enough to start on.>

## Open questions
<Blockers and decisions waiting on someone.>
```

Seed "Current state" from what you observed. Leave the others for the user if unknown.

## TASKS.md

```markdown
# Tasks

## Now
## Next
## Later
## Done
```

One line per task, newest at the top of each section. Seed "Later" from `TODO` comments
that describe features rather than debt.

## DECISIONS.md

```markdown
# Decisions

## <YYYY-MM-DD> - <Short title>
**Context:** <the situation and the constraint>
**Decision:** <what was chosen>
**Alternatives:** <what else was considered and why not>
**Consequences:** <what this makes easier and harder>
```

Seed only with decisions whose reasoning is actually written down somewhere (a comment, a
commit message, an existing doc). Noticing that the project uses a library is not a
recorded decision; do not invent the rationale.

## KNOWN_ISSUES.md

```markdown
# Known issues

## <Short title>
**Symptom:** <what goes wrong, and how to reproduce>
**Tried:** <attempts that did not work, so nobody repeats them>
**Status:** <open | workaround in place | wontfix> - <YYYY-MM-DD>
```

Seed from `FIXME`, `BUG` and `XXX` comments and from skipped or expected-failure tests,
citing the file and line.

## docs/ARCHITECTURE.md

```markdown
# Architecture

## Overview
<One paragraph: the shape of the system.>

## Components
| Component | Path | Responsibility |
|---|---|---|

## How a request flows
<Follow one typical request, command or job from entry to result, naming the files.>

## External dependencies
<Databases, queues, third-party services, and what each is used for.>

## Constraints
<Design limits the code visibly works within.>
```

Add a Mermaid diagram when there are more than a handful of components.

## docs/TESTING.md

```markdown
# Testing

## Running tests
<All tests, a single file, a single test, watch mode.>

## Layout
<Where tests live and how they map to source.>

## Kinds of tests
<Unit, integration, end-to-end: what each covers and what it needs running.>

## Fixtures and test data
## What CI runs
## Adding a test
<The pattern to copy, pointing at one good existing example.>
```

## docs/FEATURES.md

```markdown
# Features

| Feature | What it does | Entry point | Status |
|---|---|---|---|
```

Follow the table with a short section for any feature whose workflow needs explaining.

## docs/API_REFERENCE.md

If an OpenAPI or GraphQL schema exists, link to it and summarise the groups; do not copy
it out, since the schema is the source of truth and a copy will drift.

```markdown
# API reference

## <Group>
| Method | Path | Auth | Purpose | Handler |
|---|---|---|---|---|
```

For a library, list the public exports with signature and a one-line purpose instead.

## docs/DATABASE_SCHEMA.md

The migrations or schema file are the source of truth; name them at the top and describe
what they do not say on their own. Do not copy out every column, since a copy will drift.

```markdown
# Database schema

Source of truth: `<path to migrations or schema file>`

## Tables
| Table | Purpose | Key columns | Relates to |
|---|---|---|---|

## Notes
<Constraints, indexes and conventions that matter when changing the schema:
soft deletes, tenant scoping, enum values, columns that look unused but are not.>

## Changing the schema
<The migration command and workflow the repo actually uses.>
```

A Mermaid ER diagram helps once there are more than a handful of tables.

## docs/DATA_FLOW.md

```markdown
# Data flow

## <Flow name>
**Trigger:** <request, event, schedule>
**Steps:** <ordered, naming the component for each>
**Ends in:** <stores written, events emitted, responses returned>
**On failure:** <retries, dead-letter handling, what is left half-done>
```

A Mermaid sequence diagram per flow usually explains more than the prose.

## docs/SECURITY.md

```markdown
# Security

## Authentication
## Authorization
## Secrets and configuration
<Which variables are secret and where they are loaded. Names only.>

## Trust boundaries
<Where untrusted input enters and where it is validated.>

## Reporting a vulnerability
TODO: contact and process
```

Describe what the code does. Do not assert protections you did not see implemented.

## docs/DEPLOYMENT.md

```markdown
# Deployment

## Environments
## Build
## Deploy
<The steps as CI or the scripts actually perform them.>

## Configuration
## Rollback
```

## docs/TECH_DEBT.md

```markdown
# Tech debt

| Item | Location | Why it is debt | Suggested fix | Priority |
|---|---|---|---|---|
```

Seed from `TODO`, `HACK` and `shortcut:` comments, citing file and line. Things that are
broken belong in `KNOWN_ISSUES.md`; this file is for things that work but should not stay
as they are.

## CHANGELOG.md

```markdown
# Changelog

## [Unreleased]

## [<version>] - <YYYY-MM-DD>
### Added
### Changed
### Fixed
```

Seed released versions from git tags if there are any. Otherwise write only `[Unreleased]`.

## CONTRIBUTING.md

```markdown
# Contributing

## Setup
<Link to the README quick start.>

## Branches and commits
<The convention the git history actually follows, if it is consistent.>

## Before opening a pull request
<The checks to run, matching CI.>

## Review
```

## Nested AGENTS.md

Place one in a subdirectory only for what differs from the root file: that area's
commands, its conventions, its gotchas. Keep it to a screenful and do not repeat the root.
