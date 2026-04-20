# Endless Halls Helper — Project Instructions

This file is the starting point for each Claude conversation. Update it at the end of each session with the current project state.

## What this project is

A static web app that helps players solve the **Endless Halls** maze (a WoW mount-hunt event). The user feeds in paths they have explored as text; the app brute-forces all 64 cells of an 8×8 wrap-around grid as candidate starting positions, keeps only the ones consistent with every path, and — once a unique solution remains — draws the map and computes shortest paths between any two clicked rooms.

## Tech stack

- Pure client-side: HTML + CSS + JavaScript, no build step, no backend.
- jQuery 2.1.3 is the only runtime dependency ([libs/jquery-2.1.3.min.js](libs/jquery-2.1.3.min.js)).
- Rendering uses one `<canvas>` per cell inside an 8×8 `<table>`. Room art is rotated SVG.

## File layout

- [index.html](index.html) — page shell, instructions, example buttons.
- [js/main.js](js/main.js) — all solver + renderer logic (~1100 lines). Entry point: `UserInterface.prototype.init` at [js/main.js:101](js/main.js#L101).
- [js/utils.js](js/utils.js) — generic helpers (inheritance, number/string utils, image pixels). Loaded but lightly used.
- [js/popup.js](js/popup.js) — popup/tooltip helpers. **Not loaded from index.html.**
- [main.css](main.css) — layout/fonts. `@import url('reset.css')` is commented out.
- [reset.css](reset.css) — CSS reset. **Not linked from anywhere.**
- [images/](images/) — room-tile SVGs (1/2/3/4-exit variants), orb / rune / death / valid icons, plus `saturday.png` referenced in instructions.

## Input format (in the `<textarea>`)

One path per line; each path is a contiguous exploration:
- `N` / `S` / `E` / `W` — one step in that direction
- `#X` — rune room, `X` ∈ `R G B Y P`
- `$X` — orb room, same color set
- `&T` — trap (teleport) room

Example: `#YSSSSWSWW#G` = start at yellow rune, walk S·S·S·S·W·S·W·W, arrive at green rune.

## Solver model (high level)

- The grid wraps around as a torus **with a twist**: leaving an edge flips the perpendicular coordinate by 4 (`flipSides` at [js/main.js:1017](js/main.js#L1017)).
- "Overlap rooms" have all four exits but force straight-through traversal (N↔S or E↔W only, no turning).
- `_map_room_distance` stores **two** distances per cell `[vertical, horizontal]` to handle overlap-room BFS correctly.
- `findValidSquares` ([js/main.js:197](js/main.js#L197)) tries each of the 64 cells as a start, running `testPaths` / `testSinglePath`. A unique survivor = the solved starting cell. Zero survivors → alert about bad input.
- Shortest path ([js/main.js:671](js/main.js#L671)): BFS fills distances (skipping trap rooms), then `showPath` walks back from the target greedily, respecting overlap-room axis constraints.

## Conventions / collaboration notes

- When the user asks about a bug or feature, **answer the question but do not change code** until the user confirms.
- Before big updates or structural changes, remind the user to push to GitHub.
- At end of session / on commit, refresh this file and [README.md](README.md), then summarize changes and suggest a commit message.

## Known issues / rough edges (as of 2026-04-20)

A code review identified several items to address in future sessions:
- HTML-string build in `init` drops the top column-header row and the closing `</table></div></div>` due to missing `+` / `+=` on two lines ([js/main.js:125](js/main.js#L125), [js/main.js:140](js/main.js#L140)).
- `console.err` typo at [js/main.js:491](js/main.js#L491) (should be `console.error`).
- Several implicit globals (`for (x=0; ...)` without `var`) inside `getRoomPosition`, `paintMap`, `tracePath`, and `next_path = paths.shift()...`.
- `retry_counter > 20` in `testPaths` returns `true`, which can mark genuinely unsolvable configurations as valid ([js/main.js:431](js/main.js#L431)).
- `resizeWindow` is bound on window resize but never defined ([js/main.js:152](js/main.js#L152)).
- `$('#main_canvas').bind('mousewheel', null)` binds to a non-existent element ([js/main.js:146](js/main.js#L146)).
- `reset.css` exists but is neither `@import`ed nor `<link>`ed.
- [README.md](README.md) was a template stub and has now been rewritten to describe this project.

## Session log

- **2026-04-20** — First real Claude session on this repo. Inspected the codebase, replaced the placeholder `PROJECT_INSTRUCTIONS.md` and `README.md`, and produced a code-review of [js/main.js](js/main.js). No source code has been modified yet.
