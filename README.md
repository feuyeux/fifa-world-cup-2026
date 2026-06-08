# FIFA World Cup 2026 — Squad Posters

A content pipeline for producing **26-player squad roster posters** for every
team in the 2026 FIFA World Cup.

The repository is organized around three inputs:

- Per-team **squad files** in `squads/*.md`.
- Per-team **kit reference images** in `team_jerseys/*.png`.
- A master Chinese **poster prompt template** in `squads/_prompt.md`.

There is no renderer in this repository. Posters are produced by feeding the
team squad file, the matching kit reference image, and the prompt constraints
to an external image generator. Consistency comes from the fixed prompt rules,
the 4 x 7 grid, and strict filename matching.

## Groups 2026年世界杯·D·土耳其队26人名单

1. Group A 第一组: Mexico 墨西哥, South Africa 南非, South Korea 韩国, Czechia 捷克
1. Group B 第二组: Canada 加拿大, Bosnia-Herzegovina 波黑, Qatar 卡塔尔, Switzerland 瑞士
1. Group C 第三组: Brazil 巴西, Morocco 摩洛哥, Haiti 海地, Scotland 苏格兰
1. Group D 第四组: United States 美国, Paraguay 巴拉圭, Australia 澳大利亚, Türkiye 土耳其
1. Group E 第五组: Germany 德国, Curacao 库拉索, Ivory Coast 科特迪瓦, Ecuador 厄瓜多尔
1. Group F 第六组: Netherlands 荷兰, Japan 日本, Sweden 瑞典, Tunisia 突尼斯
1. Group G 第七组: Belgium 比利时, Egypt 埃及, Iran 伊朗, New Zealand 新西兰
1. Group H 第八组: Spain 西班牙, Cape Verde 佛得角, Saudi Arabia 沙特阿拉伯, Uruguay 乌拉圭
1. Group I 第九组: France 法国, Senegal 塞内加尔, Iraq 伊拉克, Norway 挪威
1. Group J 第十组: Argentina 阿根廷, Algeria 阿尔及利亚, Austria 奥地利, Jordan 约旦
1. Group K 第十一组: Portugal 葡萄牙, Congo DR 刚果民主共和国, Uzbekistan 乌兹别克斯坦, Colombia 哥伦比亚
1. Group L 第十二组: England 英格兰, Croatia 克罗地亚, Ghana 加纳, Panama 巴拿马

## Repository Layout

```text
.
├── squads/
│   ├── [A-L]_<Team>.md     # 48 team squad tables, one file per team
│   └── _prompt.md          # Master Chinese prompt for team squad posters
├── squads_11/
│   └── [A-L]_<Team>.md     # 48 team starting 11 tables, one file per team
├── group_jerseys/          # Group-level kit reference images, A.png through L.png
├── group_jerseys-ii/       # Second group-level kit reference set, A.png through L.png
├── team_jerseys/           # Per-team kit reference images
├── team_squads/            # Generated 26-player squad poster outputs
├── schedule/
│   ├── match schedule.md   # Master 2026 World Cup match schedule (BJT)
│   └── schedule_poster.png # High-fidelity cropped PNG schedule poster output
└── prepare_prompts.py      # Helper for preparing batched prompt files
```

## Squad Files
奶黄色背景，RGB(248, 240, 228)，HEX #F8F0E4
Each team has one Markdown file named with its group letter and English team
name, for example `squads/A_Czechia.md`.

The table format is:

```markdown
### Czech Republic 捷克

| 号码 | 位置 | 姓名 | 出生日期 | 俱乐部 |
| :--- | :--- | :--- | :--- | :--- |
| 1 | GK | 英德日赫·斯塔尼克 / Jindřich Staněk | 1996-04-27 | 捷克 布拉格斯拉维亚 / Slavia Prague |
```

Rules:

- Use all and only the players in the selected squad file.
- Keep the `号码`, `位置`, `姓名`, `出生日期`, and `俱乐部` columns intact.
- Position values must be `GK`, `DF`, `MF`, or `FW`.
- The filename is authoritative for group and team identity.

## Starting 11 Files

Each team has a starting 11 file under `squads_11/` containing exactly 11 players selected from the original 26-player squad file in `squads/` representing the standard starting lineup.
The table format is identical to the squad files, containing exactly 1 GK and 10 outfield players, ordered by position (GK -> DF -> MF -> FW) and sorted by shirt number.

## Kit References

Use the matching image in `team_jerseys/` for team-specific squad posters. The
basename must match the squad file:

```text
squads/L_Croatia.md
team_jerseys/L_Croatia.png
```

The prompt treats the kit image as the visual authority. Outfield players
wear the referenced home kit unless the user explicitly requests the away kit.
Goalkeepers must use a distinct goalkeeper kit and must not reuse the outfield
home or away shirt.

`group_jerseys/` and `group_jerseys-ii/` contain group-level kit references
named `A.png` through `L.png`.

## Poster Prompt

`squads/_prompt.md` is the source of truth for poster generation. Key fixed
constraints include:

- Output size: `2520 x 1800 px`, 7:5.
- Rigid `4 x 7` grid of 28 equal cells.
- Cells 1-26: one player per cell, filled left to right and top to bottom.
- Cell 27: national FA emblem.
- Cell 28: 2026 FIFA World Cup logo.
- Player order: GK, then DF, then MF, then FW; sort by shirt number within
  each position.
- No title, subtitle, header, banner, single-player feature shot, duplicate
  player, or mixed-kit output.

Do not loosen the negative constraints. The prompt is intentionally strict to
make repeated generations comparable.

## Prompt Batching

`prepare_prompts.py` parses the per-team squad Markdown files and prepares
batched prompt Markdown files for external generation.

The helper expects:

- Squad files under `squads/`.
- Matching team kit references under `team_jerseys/` directory.

## Match Schedule Poster

The repository also provides a pre-rendered high-fidelity, beautifully spaced, portrait match schedule poster.

- **Source File**: `schedule/match schedule.md` — the master BJT-timezone schedule of the 104 tournament matches.
- **Output Poster**: `schedule/schedule_poster.png` — the rendered high-fidelity cropped PNG poster showing the complete tournament calendar.

## Source Data

Squad rosters are taken from public Wikipedia squad pages.


## Working Notes

- PNGs are tracked through Git LFS, as configured in `.gitattributes`.
- Keep squad files, team kit references, and generated team poster filenames
  in sync by basename.
----

1. <https://en.wikipedia.org/wiki/2026_FIFA_World_Cup_squads>
2. <https://zh.wikipedia.org/zh-cn/2026年國際足協世界盃參賽球員名單>
