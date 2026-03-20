# Repository Guidelines

## Project Structure & Module Organization

This repository is organized around installable agent skills. The root contains
project metadata in `README.md` and `LICENSE`. Skills live under
`skills/<skill-name>/`. Each skill should include a `SKILL.md` with frontmatter
(`name`, `description`) and may include supporting files such as
`agents/openai.yaml`.

Current example:

- `skills/cleanup-codex-rules/SKILL.md` - skill instructions and workflow
- `skills/cleanup-codex-rules/agents/openai.yaml` - UI metadata for the skill

## Build, Test, and Development Commands

There is no build system in this repository today. Work is primarily editing
Markdown and YAML files.

- `rg --files` lists repository files quickly
- `sed -n '1,200p' skills/<skill-name>/SKILL.md` previews a skill document
- `git diff --stat` summarizes pending changes
- `git log --oneline` shows recent commit style

To validate the install path shown in the docs, use the pattern from
`README.md`:

```bash
npx skills add https://github.com/favoyang/agent-skills/tree/main/skills/cleanup-codex-rules
```

## Coding Style & Naming Conventions

Use concise, instructional prose in Markdown. Prefer short paragraphs and flat
lists. Keep YAML minimal and readable with two-space indentation. Name skill
directories in lowercase kebab-case, for example `cleanup-codex-rules`. Keep
file names conventional: `SKILL.md` for the skill definition and
`agents/openai.yaml` for agent-specific metadata.

## Testing Guidelines

No automated test suite is configured yet. Validate changes by checking
Markdown rendering, frontmatter correctness, and referenced paths. For new
skills, confirm that the GitHub-backed install path is correct and that any
example commands are runnable as written.

## Commit & Pull Request Guidelines

Recent commits use short, imperative subjects such as `Add MIT license` and
`Initial skill: cleanup-codex-rules`. Follow that pattern and keep commits
focused on one change. Pull requests should include a brief summary, note any
new skill paths added under `skills/`, and mention manual verification steps.
Include screenshots only if a change affects rendered UI metadata or external
presentation.
