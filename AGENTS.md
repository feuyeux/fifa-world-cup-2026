# Repository Guidelines

## Project Structure & Module Organization

This repository is a content pipeline for FIFA World Cup 2026 squad poster
generation and tournament match schedule poster generation. Squad data lives in `squads/[A-L]_<Team>.md`, with one Markdown
table per team. The starting 11 roster summaries are in `squads_11/[A-L]_<Team>.md`.
The master poster prompt is `squads/_prompt.md`. Kit and generated image assets
are organized by scope: `group_jerseys/`, `group_jerseys-ii/`,
`team_jerseys/`, and `team_squads/`.

For match schedule posters, the master BJT schedule data is in `schedule/match schedule.md`. The helper script is not kept in this repository, and the final pre-rendered poster output is saved in `schedule/schedule_poster.png`.

There is one helper script:
- `prepare_prompts.py`: generate batch prompt files from squad tables.

## Build, Test, and Development Commands

There is no application build step. Work is mostly file validation, prompt
preparation, and schedule poster rendering.

- `python3 prepare_prompts.py`: generate batch prompt files from squad tables.
  Check paths in the script first; it may still reference the legacy
  `jerseys/teams/` layout.
- `git status --short`: inspect changed assets and documentation before
  committing.
- `git diff -- README.md AGENTS.md squads/_prompt.md`: review text changes
  without dumping large image diffs.

## Coding Style & Naming Conventions

Use Markdown for documentation and squad data. Keep headings descriptive and
tables stable. Squad filenames must follow `Group_Team.md`, for example
`A_Czechia.md` or `K_Congo DR.md`; matching assets should reuse the same
basename, for example `team_jerseys/A_Czechia.png`.

Python code should use 4-space indentation, UTF-8 file handling, and clear
function names. Avoid broad rewrites unless the directory contract changes.

## Testing Guidelines

No formal test framework is configured. Validate changes manually:

- Confirm Markdown fences and tables are closed.
- Ensure each squad table (under `squads/` and `squads_11/`) has only `GK`, `DF`, `MF`, and `FW` positions.
- Ensure each starting 11 table under `squads_11/` contains exactly 11 players (1 GK, 10 outfield).
- Ensure player details in `squads_11/` match those in `squads/` exactly.
- When changing `prepare_prompts.py`, run it and inspect generated batch
  output before committing.

## Commit & Pull Request Guidelines

Recent commits use short imperative summaries such as `Add README and regroup
per-group jersey prompts` and `Reorganize jersey and squads assets`. Keep
commit subjects concise and specific; avoid vague messages like `up` or `fmt`
unless the change is truly mechanical.

Pull requests should describe the changed data, prompt, or asset set; mention
any regenerated images; and include sample outputs or screenshots when visual
assets change. Note any source-data updates in the PR description.

## Asset & Configuration Notes

PNG and other media files are tracked with Git LFS via `.gitattributes`.
Do not commit `.DS_Store`, local IDE folders, SQLite files, or local agent
state. Keep squad files, kit references, and generated poster outputs aligned
by basename.
