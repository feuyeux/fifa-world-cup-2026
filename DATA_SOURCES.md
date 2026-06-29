# Data Sources

Authoritative provenance for the data in this repository. Last audit:
**2026-06-29** (group stage complete).

## Squads (players)

| File | Source | Captured | Refresh |
|------|--------|----------|---------|
| `squads/[A-L]_*.md` (48 teams × 26 players) | [Wikipedia: 2026 FIFA World Cup squads](https://en.wikipedia.org/wiki/2026_FIFA_World_Cup_squads) + ESPN API | 2026-06-29 | Final (frozen at FIFA 2026-06-11 announcement) |

The squad `md` is the **single source of truth**; the `首发` (starter),
`出场` (caps) and `进球` (international goals) columns are merged in
from the ESPN athlete feed. No separate `squads_json/` or `squads_11/`
directory — those were consolidated into `squads/*.md` on 2026-06-29.

**Why Wikipedia as primary source:** the Wikipedia article is maintained
in near-real-time as each national federation publishes their final 26-man
roster, and exposes structured tables (number, position, date of birth,
club, caps, goals) that map 1:1 to our schema.

**Why not FIFA.com directly:** `api.fifa.com` exposes match results
(used below) and the match calendar, but does not expose a public
roster/lineup endpoint. The official squad PDFs are not machine-readable.

## Group-stage match results and appearances

| Data | Source | Captured | Refresh |
|------|--------|----------|---------|
| Match scores (72 group games + 1 R32) | [api.fifa.com](https://api.fifa.com/api/v3/calendar/matches?idcompetition=17&from=2026-06-01&to=2026-07-31) | 2026-06-29 | After each match day |
| Per-player lineups, starter/sub, jersey # | [ESPN site API](https://site.api.espn.com/apis/site/v2/sports/soccer/fifa.world/summary?event={matchId}) | 2026-06-29 | After each match day |
| Match formation, attendance, officials | ESPN (same endpoint) | 2026-06-29 | After each match day |

**ESPN is the primary source for player appearances** because FIFA's API
does not expose lineup data. ESPN provides:
- `rosters[].roster[]` with `starter`, `subbedIn`, `subbedOut`,
  `jersey`, `position.abbreviation`, `formationPlace`, `athlete.displayName`
- Per-match 26-man registration (some teams registered 23-25 due to
  injury; cells in `squads/*.md` will show `-` for those players)
- Inferred formation string (e.g. `4-2-3-1`) in `rosters[].formation`

**Coverage gaps:** 34 of 73 matches have 49-51 players listed instead of
the expected 52. This reflects real-world squad decisions (injured
players omitted from the gameday roster). For these matches, the
corresponding `squads/*.md` cells are marked `-` (did not participate).

## What is NOT auto-pulled (manual sources)

| Data | Source | Cadence |
|------|--------|---------|
| Chinese transliterations of player + club names | Hand-curated by repository owner; preserved in `squads/_prompt.md` examples | Stable |
| Starting 11 selection | Read the `首发` column in `squads/*.md` (Y = starter, - = bench); for ESM/Sub appearances use the per-opponent `vs X` cells | Re-runnable |
| Knockout round posters (`16/knockout_*.png`, etc.) | Image generation pipeline; not in scope of this document | Per round |
| Player portrait photos (`players/`, `player_photos/`) | Image generation pipeline | Per roster announcement |

## Known discrepancies (intentional)

Sixteen (16) players in the Wikipedia 2026 squad list do not match the
pre-existing `squads/*.md` entries by exact name. These fall into two
buckets:

1. **Same person, different spelling / nickname** (13 cases): kept the
   existing Chinese + English spelling from the prior `squads/*.md` to
   avoid breaking poster renders that depend on string matching. Examples:
   `Kaku` ↔ Alejandro Romero Gamarra (Paraguay), `Pico` ↔ Pico Lopes
   (Cape Verde), `Leo Østigård` ↔ Leo Ostigard (Norway).

2. **Likely different person** (3 cases): kept the existing
   `squads/*.md` entry because the new Wikipedia entry may reflect a
   late roster swap we cannot verify independently.
   - Qatar: `Tahsin Jamshid` (Wiki) vs `Tahsin Mohammed` (existing)
   - Iran: `Dennis Eckert` (Wiki) vs `Dennis Dargahi` (existing)
   - Jordan: `Mohammad Taha` (Wiki) vs `Mohammad Aldaoud` (existing)

3. **Position disagreement** (2 cases): Norway
   - `Antonio Nusa`: Wiki MF vs existing FW
   - `Julian Ryerson`: Wiki DF vs existing FW

## Verification commands

```bash
# Re-fetch group-stage match results
curl -sL "https://api.fifa.com/api/v3/calendar/matches?idcompetition=17&from=2026-06-01&to=2026-07-31" -H "User-Agent: Mozilla/5.0" -o /tmp/fifa_matches.json

# Re-fetch a single match summary (lineup data)
curl -sL "https://site.api.espn.com/apis/site/v2/sports/soccer/fifa.world/summary?event=760415" -H "User-Agent: Mozilla/5.0" -o /tmp/match.json

# Validate local squads: 26 players, valid pos/dob, every cell accounted for
python3 -c "from pathlib import Path; import re; \
  [print(f, 'OK' if all( \
    (lambda r: len(r)==26 and all(c[1] in ('GK','DF','MF','FW') and re.match(r'\d{4}-\d{2}-\d{2}', c[3]) for c in r))([l for l in f.read_text(encoding='utf-8').splitlines() if l.strip().startswith('|') and '---' not in l][1:]) \
   else 'FAIL') for f in sorted(Path('squads').glob('[A-L]_*.md'))]"
```

## Future work

- **Knockout stage (R32, R16, QF, SF, Final)**: ESPN's scoreboard endpoint
  exposes R32 (4 matches); for the deeper rounds a separate
  `site.api.espn.com/apis/site/v2/sports/soccer/fifa.world/scoreboard?dates=...`
  call will be needed once matches are scheduled. Lineup availability
  depends on match completion.
- **Player photo URL hydration**: `squads/*.md` does not currently
  carry a photo column. Hooking the FIFA picture API
  (`https://api.fifa.com/api/v3/picture/players-{format}-{size}/{IdPlayer}`)
  is straightforward once player IDs are mapped from the ESPN athlete IDs.
- **Live data refresh job**: a `tools/refresh_squads.py` script that
  re-pulls the Wikipedia squads + ESPN summaries and applies only the
  diff to `squads/*.md`. Track this in `tools/` once stable.
