---
name: repo-docs
description: Create or refresh a project's documentation set (AGENTS.md, CLAUDE.md, README, HANDOFF, TASKS, DECISIONS, KNOWN_ISSUES, docs/ARCHITECTURE, TESTING, API reference and more) by analyzing the repository, or scaffold starter docs for an empty or brand-new project. Offers three tiers - basic, standard, full. Use this whenever the user asks to document a repo, set up or initialize project docs, generate AGENTS.md or CLAUDE.md, add handoff / progress / decision / known-issues files, bootstrap docs for a new project, or says things like "create the docs", "document this codebase", "set up the project files" - even if they name no tier and no specific file.
---

# Repo Docs

Write the documentation a project needs so that a person or a coding agent (any tool, not
just one vendor's) can pick it up cold: what it is, how to run it, how it is built, where
work stopped, and why things are the way they are.

The skill takes one optional argument: `basic`, `standard` or `full`. If the user gave
none, ask which tier they want and recommend `standard`. When there is nobody to ask (a
non-interactive run), use `standard` and say so in the report.

## Tiers

Each tier includes the one before it.

| Tier | Adds |
|---|---|
| **basic** | `README.md`, `AGENTS.md`, `CLAUDE.md`, `HANDOFF.md` |
| **standard** | `TASKS.md`, `DECISIONS.md`, `KNOWN_ISSUES.md`, `docs/ARCHITECTURE.md`, `docs/TESTING.md` |
| **full** | `docs/FEATURES.md`, `docs/API_REFERENCE.md`, `docs/DATABASE_SCHEMA.md`, `docs/DATA_FLOW.md`, `docs/SECURITY.md`, `docs/DEPLOYMENT.md`, `docs/TECH_DEBT.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, nested `AGENTS.md` files |

The tier is a ceiling, not a checklist. A doc is written only when the repo gives it
something to say, because a doc full of guesses or placeholders is worse than no doc: it
looks authoritative, nobody maintains it, and the next reader trusts it.

| Doc | Write it when the repo has |
|---|---|
| `docs/ARCHITECTURE.md` | source code |
| `docs/TESTING.md` | tests, or a configured test runner |
| `docs/FEATURES.md` | more user-facing features or workflows than a short README list can hold |
| `docs/API_REFERENCE.md` | HTTP routes, an RPC or GraphQL schema, an OpenAPI spec, or a library's public API |
| `docs/DATABASE_SCHEMA.md` | migrations, ORM models, or a schema file |
| `docs/DATA_FLOW.md` | queues, event buses, scheduled jobs, pipelines, or calls across several services |
| `docs/SECURITY.md` | authentication, sessions or tokens, permissions, secrets, or personal data |
| `docs/DEPLOYMENT.md` | a Dockerfile, a deploy job in CI, infrastructure code, or hosting-platform config |
| `CHANGELOG.md` | git tags, or a package that is actually published (a publish step in CI or scripts). A version field alone is not evidence: nearly every manifest has one |
| `CONTRIBUTING.md` | several contributors in the git history, or a licence that invites outside work |
| nested `AGENTS.md` | a sub-area (say `frontend/` and `backend/`) whose stack or conventions differ from the root |

## Two kinds of doc

Treat these differently; it decides what you may touch on a later run.

**Derived docs** describe the code: `README.md`, `AGENTS.md`, and everything under `docs/`
except `TECH_DEBT.md`. Their content comes from the repository, so every statement should
trace back to a file you read. They can be refreshed when the code changes.

**Log docs** record things the code cannot tell you: `HANDOFF.md`, `TASKS.md`,
`DECISIONS.md`, `KNOWN_ISSUES.md`, `docs/TECH_DEBT.md`, `CHANGELOG.md`. People and agents
fill them in over time. Create each once from its template, seed it with whatever the repo
does evidence (see the templates), and after that leave it alone. An empty log doc is fine
and is the one exception to "no placeholders": it tells everyone where to write.

## Workflow

### 1. Survey the repo

Read before writing. Look at:

- the file tree, skipping ignored paths, dependencies and build output
- manifests and lockfiles (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`, ...) for the stack and the real commands
- entry points, routing or API definitions, and the main modules
- database migrations, ORM models and schema files
- tests and test configuration
- CI workflows, Dockerfiles, infrastructure and hosting config
- `.env.example` or equivalent for configuration names. Do not open real `.env` files or other secret stores; the variable names are all a doc needs, and values must never end up in one
- existing docs and agent instruction files (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.cursor/rules`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`)
- the git log and tags, if it is a git repository

### 2. Empty or existing?

A repo is **empty** when it has no source code yet, even if it holds a licence, a
`.gitignore` or a stub README.

**Empty repo.** Ask the user up to three things, in one message: what the project is in a
sentence, the intended stack, and what kind of thing it is (app, library, service, CLI).
Then write the basic files plus the log docs of the chosen tier. Skip the other derived
docs and say so: there is no architecture or API to describe yet, and inventing one would
commit the project to a design nobody chose. Tell the user to run the skill again once
there is code.

**Existing repo.** Go to step 3.

### 3. Plan, then write

For every doc in the tier, decide one of:

- **create** - it does not exist and the repo has evidence for it
- **skip** - nothing in the repo to base it on
- **keep** - it already exists

Give the user the plan as a short list, then write the `create` files without waiting,
using the outlines in [references/templates.md](references/templates.md). Read only the
templates you are about to use.

Stop and ask only where an existing file is involved (next section).

### 4. Report

Finish with a table: file, action (created / skipped / kept), and for skipped files the
reason in a few words. List any `TODO:` markers you left so the user knows what to fill in.

## Rules, and why they exist

**Never overwrite an existing doc.** Hand-written docs hold knowledge that cannot be
rebuilt from the code. If an existing derived doc is missing sections or contradicts the
code, show the proposed additions or corrections and apply them only when the user agrees.
Never edit an existing log doc.

**Write only what you verified.** Commands come from manifests, scripts and CI, not from
what the stack usually uses. An agent will run the commands in `AGENTS.md` literally, so a
plausible wrong command costs more than a missing one. Where you could not find something
that the doc needs, write `TODO: <what is missing>` instead of a guess.

**Record the problems you find; do not fix them.** Checking commands against the repo often
turns up something broken: a test script that fails, a build step with nothing to install
from. That is exactly what the next reader needs to know, and it is lost if it only appears
in your final message. Add it to `KNOWN_ISSUES.md` when you are creating that file, marked
as found during this pass and as either reproduced or only read from the code, so nobody
mistakes it for a report from the team. If `KNOWN_ISSUES.md` already exists or the tier has
none, put it in the report instead. Leave the code alone: the task is documentation.

**`AGENTS.md` is the single source of agent instructions.** It is the file most coding
agents read natively. `CLAUDE.md` holds one line, `@AGENTS.md`, so that Claude Code loads
the same content and the two can never drift. If the repo already has a populated
`CLAUDE.md` or another tool's instruction file but no `AGENTS.md`, do not move anything on
your own: create `AGENTS.md` from the repo, and in the report suggest folding the older
file's content into it and reducing that file to a pointer.

**Keep `AGENTS.md` short** - aim for under a hundred lines. It is loaded into every agent
session, so every line costs context on every task. Put commands, layout, the conventions
a newcomer would get wrong, and links to the deeper docs. Detail belongs in `docs/`.

**State each fact once.** Setup lives in the README; other docs link to it. Duplicated
facts go stale independently and then disagree.

**Follow what the repo already does.** If decisions live in `docs/adr/` or similar, add to
that and skip `DECISIONS.md`. If docs live in a differently named folder, use it. Match the
existing heading style and tone.

**Use absolute dates** (`2026-03-14`, not "last week") in log docs; they are read months
later.

## Running it again

On a repo that already has these docs, compare each derived doc with the current code and
propose updates for anything that drifted. Create docs that now have evidence and did not
before (for example `docs/API_REFERENCE.md` after routes were added). Leave log docs
untouched.
