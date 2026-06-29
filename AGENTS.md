# Repository Guidelines

## Project Structure & Module Organization

This repository is a content pipeline for FIFA World Cup 2026 squad poster
generation and tournament match schedule poster generation. Squad data lives in `squads/[A-L]_<Team>.md`, with one Markdown
table per team. The `首发` (starter), `出场` (international caps) and
`进球` (international goals) columns come from the ESPN athlete feed and
are part of the single source of truth — there is no separate
`squads_11/` or `squads_json/` directory.
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
- Ensure each squad table under `squads/` has only `GK`, `DF`, `MF`, and
  `FW` positions.
- Ensure each squad table has 26 rows and the column header order is
  `号码 | 位置 | 姓名 | 出生日期 | 俱乐部 | 首发 | 出场 | 进球
  [ | vs <Opponent> ]*` (per-opponent columns appear once the match has
  been played).
- The starting 11 is the subset of rows where `首发 == Y`; the count
  must equal 11 (1 GK + 10 outfield) per team.
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

## Web Scraping (Firecrawl)

For pulling fresh FIFA / Wikipedia / news content into the pipeline, use
`firecrawl-py` (installed in the anaconda3 base environment).

- Library: `firecrawl-py 4.30.3` at `D:\garden\anaconda3\Lib\site-packages`.
  Invoke via `D:\garden\anaconda3\python.exe` — the default `python` on this
  host is the 3.14 pythoncore build and will NOT see the package.
- API key: stored in `FIRECRAWL_API_KEY` (configured in `~/.bashrc`, auto-loaded
  in git-bash). Never commit the key; never echo it into chat.
- Billing cycle resets monthly. Check remaining credits before large
  batch jobs:
  `curl -s https://api.firecrawl.dev/v1/team/credit-usage -H "Authorization: Bearer $FIRECRAWL_API_KEY"`.
- Minimal scrape:
  ```python
  from firecrawl import Firecrawl
  app = Firecrawl()  # reads FIRECRAWL_API_KEY from env
  r = app.scrape("https://...", formats=["markdown"])
  print(r.markdown)
  ```
- Prefer `formats=["markdown"]` for squad/profile ingestion and
  `formats=["markdown", "html"]` only when extra structure is needed; large
  pages (e.g. Wikipedia tournament articles) can return >500K chars — pipe
  through a slicer or write to disk before downstream parsing.

## Asset & Configuration Notes

PNG and other media files are tracked with Git LFS via `.gitattributes`.
Do not commit `.DS_Store`, local IDE folders, SQLite files, or local agent
state. Keep squad files, kit references, and generated poster outputs aligned
by basename.
