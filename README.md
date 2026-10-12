<p align="center">
  <img src="assets/logo.svg" width="112" alt="repo-docs logo">
</p>

<h1 align="center">repo-docs</h1>

<p align="center">
  <strong>An agent skill that writes the docs your repo actually needs,<br>and skips the ones it would have to make up.</strong>
</p>

<p align="center">
  <a href="https://github.com/Fasil-OK/repo-docs/stargazers"><img src="https://img.shields.io/github/stars/Fasil-OK/repo-docs?style=flat" alt="GitHub stars"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="MIT license"></a>
  <a href="https://agentskills.io"><img src="https://img.shields.io/badge/Agent%20Skills-compatible-4F46E5" alt="Agent Skills compatible"></a>
</p>

<p align="center">
  <a href="#install">Install</a> ·
  <a href="#usage">Usage</a> ·
  <a href="#tiers">Tiers</a> ·
  <a href="#how-it-decides-what-to-write">How it decides</a> ·
  <a href="#example">Example</a> ·
  <a href="#tested">Tested</a>
</p>

---

## Why

Ask a coding agent to "document this repo" and you usually get one of two things: a single
README, or a pile of files with plausible commands nobody ran and sections that say "TBD".
The second is worse, because the next person, or the next agent, trusts it.

`repo-docs` gives the agent a fixed set of documents, three sizes to choose from, and one
rule: **a doc is written only when the repository has evidence for it.**

- **Reads before it writes.** Commands come from your manifests, scripts and CI, not from what the stack usually uses.
- **Never overwrites.** Hand-written docs are left untouched. Changes are proposed, not applied.
- **Works with any agent.** `AGENTS.md` is the single source of agent instructions, and `CLAUDE.md` is a one-line import of it, so the two never drift.
- **Handles empty repos.** A new project gets starter files and log templates, and no invented architecture.

## Install

`repo-docs` is a plain [Agent Skill](https://agentskills.io): a folder with a `SKILL.md`. It
works in any [agent that supports the format](https://agentskills.io/clients), including
Claude Code, Codex, Cursor, Gemini CLI and GitHub Copilot.

**Any agent**, with the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add Fasil-OK/repo-docs
```

**Claude Code**, as a plugin:

```
/plugin marketplace add Fasil-OK/repo-docs
/plugin install repo-docs@repo-docs
```

**Manual:** copy the [`skills/repo-docs`](skills/repo-docs) folder into your agent's skills
directory, for example `~/.claude/skills/`.

## Usage

Name a tier:

```
/repo-docs standard
```

Or just ask:

> document this codebase
>
> set up the docs for a new project, basic
>
> create the full docs for this repo

If you give no tier, the agent asks and recommends `standard`. In a non-interactive run it
uses `standard` and says so in its report. Installed as a Claude Code plugin, the command is
`/repo-docs:repo-docs`.

## Tiers

Each tier includes the one before it.

| Tier | Adds |
|---|---|
| **basic** | `README.md`, `AGENTS.md`, `CLAUDE.md`, `HANDOFF.md` |
| **standard** | `TASKS.md`, `DECISIONS.md`, `KNOWN_ISSUES.md`, `docs/ARCHITECTURE.md`, `docs/TESTING.md` |
| **full** | `docs/FEATURES.md`, `docs/API_REFERENCE.md`, `docs/DATABASE_SCHEMA.md`, `docs/DATA_FLOW.md`, `docs/SECURITY.md`, `docs/DEPLOYMENT.md`, `docs/TECH_DEBT.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, nested `AGENTS.md` files |

## How it decides what to write

The tier is a ceiling, not a checklist. Inside it, each doc needs evidence:

| Doc | Written when the repo has |
|---|---|
| `docs/ARCHITECTURE.md` | source code |
| `docs/TESTING.md` | tests, or a configured test runner |
| `docs/FEATURES.md` | more user-facing features than a short README list can hold |
| `docs/API_REFERENCE.md` | HTTP routes, an RPC or GraphQL schema, an OpenAPI spec, or a library's public API |
| `docs/DATABASE_SCHEMA.md` | migrations, ORM models, or a schema file |
| `docs/DATA_FLOW.md` | queues, event buses, scheduled jobs, pipelines, or calls across several services |
| `docs/SECURITY.md` | authentication, sessions or tokens, permissions, secrets, or personal data |
| `docs/DEPLOYMENT.md` | a Dockerfile, a deploy job in CI, infrastructure code, or hosting config |
| `CHANGELOG.md` | a published or versioned package, or git tags |
| `CONTRIBUTING.md` | several contributors in the git history, or a licence that invites outside work |
| nested `AGENTS.md` | a sub-area whose stack or conventions differ from the root |

Docs come in two kinds, and the skill treats them differently:

- **Derived docs** describe the code (`README.md`, `AGENTS.md`, most of `docs/`). Every statement should trace back to a file, and they can be refreshed when the code changes.
- **Log docs** record what the code cannot tell you (`HANDOFF.md`, `TASKS.md`, `DECISIONS.md`, `KNOWN_ISSUES.md`, `docs/TECH_DEBT.md`, `CHANGELOG.md`). They are created once from a template, seeded from `TODO` and `FIXME` comments or skipped tests, and never rewritten.

### Rules it follows

- **No overwriting.** An existing doc is kept. Additions are shown to you and applied only if you agree.
- **No guessed commands.** If it cannot find something a doc needs, it writes `TODO: <what is missing>`.
- **No secrets.** It reads `.env.example`, never `.env`. Docs name variables, never values.
- **Short `AGENTS.md`.** Under a hundred lines, because it is loaded into every agent session.
- **Each fact once.** Setup lives in the README; other docs link to it.
- **Your conventions first.** If decisions already live in `docs/adr/`, it adds there and skips `DECISIONS.md`.

## Example

The report from a test run of `/repo-docs full` on a small Express API that already had a
hand-written `README.md` and `CLAUDE.md`:

| File | Action | Note |
|---|---|---|
| `README.md` | kept | hand-written; additions proposed |
| `CLAUDE.md` | kept | hand-written; merge into `AGENTS.md` proposed |
| `AGENTS.md` | created | 76 lines |
| `HANDOFF.md` | created | current state and open questions seeded |
| `DECISIONS.md` | created | 3 entries taken from the README, `CLAUDE.md` and a code comment |
| `KNOWN_ISSUES.md` | created | 3 problems found during the survey |
| `docs/API_REFERENCE.md` | created | 5 routes |
| `docs/DATABASE_SCHEMA.md` | created | |
| `docs/SECURITY.md` | created | |
| `docs/DEPLOYMENT.md` | created | describes the `Dockerfile`; the rest is TODO |
| `docs/TECH_DEBT.md` | created | 3 rows, from the `TODO` and `HACK` comments |
| `docs/FEATURES.md` | skipped | five routes; the API reference covers them |
| `docs/DATA_FLOW.md` | skipped | one service, no queues or jobs |
| `CONTRIBUTING.md` | skipped | private package, no licence, no git history |

The same run reported that `npm test` fails on Node 24 and that the Docker build has no
lockfile to install from. Both went into `KNOWN_ISSUES.md`.

## Tested

Three test repos, each run once with the skill and once without, graded by script:

| Test | With skill | Without skill |
|---|---|---|
| Empty repo, `standard` | 6 / 6 | 1 / 6 |
| Python CLI, no tier given | 10 / 10 | 2 / 10 |
| Express API with hand-written docs, `full` | 11 / 11 | 4 / 11 |

Read this with care. Most checks ask for the skill's own file set, which an agent without
the skill has no way to know, so the scores overstate the gap. Three results are the real
evidence:

- **Empty repo:** without the skill, the agent wrote an architecture doc for code that did not exist. With it, that doc was held back.
- **Existing docs:** without the skill, the agent edited the hand-written README. With it, the file was byte-for-byte unchanged.
- **No tier given:** with the skill, the agent used `standard` and said so.

The cost is time: the skill writes more files, so runs on existing repos took about 80
seconds longer on average. Each test ran once, so there is no measure of run-to-run variation.

## Repository layout

```
skills/repo-docs/
├── SKILL.md              tiers, workflow and rules
└── references/
    └── templates.md      an outline for each doc
```

## Contributing

Issues and pull requests are welcome. The most useful report is a case where the skill wrote
a doc your repo had no evidence for, or skipped one it should have written. Include the repo
layout and the tier you asked for.

## License

[MIT](LICENSE)
