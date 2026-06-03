# FIFA 2026 Group Kits Prompt Template

Use this prompt to generate a poster in the same structure as the reference images in this folder.

## Master Prompt

Create a high-fidelity football kits infographic poster in the same composition and visual language as the references in this folder.

Canvas and output quality:
- Exact output size: 1536 x 2048 px
- Aspect ratio: 3:4 vertical
- Ultra-detailed, high-fidelity rendering
- Print-ready clarity, sharp edges, no blur, no compression artifacts
- Consistent global lighting and clean contrast

Art style:
- Pen-and-ink illustration style (steel-pen linework)
- Fine cross-hatching and controlled stippling
- Editorial print look on warm off-white paper texture
- Precise outlines, crisp strokes, subtle grain, not painterly

Title:
FIFA WORLD CUP 2026 | GROUP L KITS

Teams (top to bottom):
- CROATIA (CRO)
- ENGLAND (ENG)
- GHANA (GHA)
- PANAMA (PAN)

Layout:
- 4 horizontal team rows
- Each row inside a rounded rectangle panel with a dark navy header strip
- Team name in white uppercase in the header strip
- Two equal kit columns per row: HOME KIT on left, AWAY KIT on right
- Thin vertical divider line at center of each row
- Equal row heights and equal cell widths
- Hard lock: all 4 team row panels must have exactly the same outer height
- Hard lock: all 4 team row panels must have exactly the same inner content height
- Hard lock: all 4 team row panels must have identical top and bottom padding
- No row is allowed to be taller or shorter than another, even by 1 px

Hard geometric constraints (must be identical in all 8 kit cells):
- Team row panel outer box: identical height for all 4 rows (fixed value per render)
- Team row panel header strip: identical height for all 4 rows
- Jersey bounding box: 260 px wide x 220 px high
- Shorts bounding box: 220 px wide x 150 px high
- Socks: two socks, each 78 px wide x 145 px high
- Jersey-to-shorts gap: 12 px
- Shorts-to-socks gap: 14 px
- Sock-to-sock gap: 22 px
- All kit stacks center-aligned to identical anchors
- No team kit may be larger or smaller than any other kit
- Front view only, upright orientation only, no perspective skew

Visual content constraints:
- Include realistic national colors, crest placement, and manufacturer logos
- Keep stroke thickness and texture density consistent across all teams
- Keep shadow strength and highlight intensity consistent across all cells
- Preserve clean whitespace around each kit stack

Negative constraints:
- No uneven scaling
- No perspective distortion
- No missing socks
- No typo in labels
- No cluttered background
- No players, no stadium, no crowd
- No poster warping, no panel misalignment

## Reusable Variables Version

Replace the values in braces:

Create a high-fidelity football kits infographic poster, exact output {WIDTH} x {HEIGHT} px, vertical layout, pen-and-ink steel-pen illustration style, matching the same panel system and hierarchy.

Title: FIFA WORLD CUP 2026 | GROUP {GROUP_LETTER} KITS

Teams (top to bottom):
- {TEAM_1} ({CODE_1})
- {TEAM_2} ({CODE_2})
- {TEAM_3} ({CODE_3})
- {TEAM_4} ({CODE_4})

Use identical fixed kit geometry in every home/away cell:
- Team row panel outer height: fixed and identical for all 4 rows
- Team row panel header height: fixed and identical for all 4 rows
- Jersey: 260 x 220 px
- Shorts: 220 x 150 px
- Socks (each): 78 x 145 px
- Vertical gaps: 12 px then 14 px
- Sock spacing: 22 px

High-fidelity requirement:
- Very sharp linework, fine micro-detail, high local contrast, no blur, no noisy edges, no compression artifacts.
