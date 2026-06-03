# FIFA World Cup 2026 — Squad Posters

A content pipeline for producing **26-player squad roster posters** for every
team in the 2026 FIFA World Cup. The pipeline is built around two pieces:

- A **canonical squad dataset** (48 teams, 26 players each) that every other
  artefact in the repository is derived from.
- A set of **AI-image-generation prompts** that pin the look of the poster
  and the home kit — the per-group jersey prompt locks the kit, the master
  squad prompt locks the layout, camera, lighting, and slot grid.

There is no renderer script in this repository: posters are produced by
feeding the prompts (and the per-group kit reference image) to an external
image generator. Determinism is enforced by the prompt constraints, not by
code.

## Repository layout

```
.
├── squads/
│   ├── _squads.md          # 48 teams × 26 players (single source of truth)
│   └── prompt.md           # Master Chinese template for the 26-headshot poster
├── jerseys/
│   ├── _groups.md          # Group A–L team rosters
│   └── A.png … L.png       # Per-group home-kit reference images
├── jersey_prompts/         # 12× 16:9 kits infographic poster prompts (prompt_a … prompt_l)
└── stuff.md                # Wikipedia source links (en + zh) for squad data
```

## Squad data

`squads/_squads.md` is the canonical input. It is organised as:

```
## Group A
### Czech Republic 捷克
| 号码 | 位置 | 姓名 | 出生日期 | 俱乐部 |
| :--- | :--- | :--- | :--- | :--- |
| 1   | GK | 英德日赫·斯塔尼克 / Jindřich Staněk | 1996-04-27 | … |
…
```

Per-team block format and column conventions live in
[[squads-table-format]] and are not re-documented here.

The 12 groups and their 48 teams are listed in `jerseys/_groups.md`. The
poster prompt refers to groups by letter; the kit image for "Group X" is
always `jerseys/X.png`. Never edit a prompt in isolation from the data — the
rosters, group letters, and kit references must stay in lock-step.

## Jersey references

`jerseys/{A..L}.png` is the **single visual authority** for what each group's
home kits look like. When generating a poster, supply the matching image as
the kit/style reference to the image generator — every player in the
resulting poster must wear the same home kit as the reference, with the same
collar, cuffs, pattern, crest, and number styling.

The matching 12 prompts live in `jersey_prompts/prompt_{a..l}.md`. They are
identical in shape — a 16:9 pen-and-ink kits infographic that lists all four
teams of the group with editable home-kit concepts.

## Squad poster prompt

`squads/prompt.md` is the Chinese-language source of truth for the poster
spec: 2400×1350, 50mm equivalent, key-light upper-left 45°, four fixed
columns with exact slot coordinates, 26 head-and-shoulders portraits only,
no single-player feature shot, no kit mix-and-match, no core-player
enlargement. **Do not loosen the negative constraints** — the whole point
of the pipeline is multi-run consistency.

To produce a poster for a given team, copy the template and fill in:

- **Team name** (e.g. 捷克队 / Czech Republic)
- **Group letter** (e.g. B → 引用 `jerseys/B.png`)
- **Core player / captain** name, for label-only highlight
- **Primary and secondary team colours**
- **The 26-player roster** as it appears in `squads/_squads.md`, split into
  GK (3) / DF (8) / MF (10) / FW (5)

Run through the four-step generation flow (structure → layout → kit
consistency → final render) and check every item on the final acceptance
list before publishing.

## Source data

Squad rosters are taken from the public Wikipedia pages for the 2026 FIFA
World Cup squads; the canonical URLs are recorded in `stuff.md`. Update that
file if you change sources.

## Working notes

- All PNGs are tracked via Git LFS (see `.gitattributes`).
- `.antigravitycli/`, `.superpowers/`, and any `*.sqlite*` are local-only —
  see `.gitignore`.
- `jerseys/prompt_*.md` (legacy per-group prompts at the repo root) was
  superseded by `jersey_prompts/prompt_*.md`; do not edit the legacy copies.
