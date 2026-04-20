# Endless Halls Helper

A browser-based solver for the **Endless Halls** maze (a World of Warcraft mount-hunt event). Feed in the paths you have explored as short text strings and the app will deduce your unique 8×8 map, draw it, and compute the shortest route between any two rooms you click.

Open `index.html` in a browser — no build step required.

## What it does

- Accepts exploration paths in a compact text format (one path per line).
- Brute-forces every cell of an 8×8 torus-with-twist grid as a candidate starting room and keeps only those consistent with every path you supplied.
- When a single starting cell remains, renders the full map: colored backgrounds for orb/rune rooms, rotated exit tiles for corridors, junctions and overlap rooms.
- Click any two rooms on the solved map to animate the shortest path between them (trap rooms are avoided, overlap rooms force straight-through traversal).

## Input format

Type paths into the textarea, one per line:

| Symbol | Meaning |
|---|---|
| `N` / `S` / `E` / `W` | One step in that direction |
| `#X` | Rune room of color `X` |
| `$X` | Orb room of color `X` |
| `&T` | Trap (teleport) room |
| colors | `R` red, `G` green, `B` blue, `Y` yellow, `P` purple |

Example — starting at the yellow rune, walking S·S·S·S·W·S·W·W and arriving at the green rune:

```
#YSSSSWSWW#G
```

Add more lines as you explore. Each new path must contain at least one already-known special room so the solver can anchor it to the existing map.

The page ships with a worked example maze and step-by-step "View Example" buttons that reproduce progressive stages of a solve.

## How it works

The map is an 8×8 torus with a twist: stepping off an edge wraps to the opposite edge **and** shifts the perpendicular coordinate by 4. Overlap rooms (all four exits open) can only be traversed straight through, which the BFS honors by tracking vertical and horizontal arrival distances separately.

Under the hood:

- `findValidSquares` tests each of the 64 cells as the starting room and marks consistent ones with a checkmark.
- `testSinglePath` walks each input path through the candidate map, failing fast on ID collisions or contradictions.
- When exactly one starting cell remains, `paintMap` draws the result onto per-cell `<canvas>` elements using rotated SVG tiles.
- Clicking two rooms kicks off a BFS followed by a greedy backtrack (`showPath` / `takeStep`) that animates the route.

## Tech

Pure HTML + CSS + JavaScript, plus jQuery 2.1.3. No build, no backend, no dependencies to install.

## Tips for mapping

- Map in small sections starting from a special room, and always backtrack to confirm you were not teleported by the trap.
- If the map starts looking wrong, discard uncertain paths and restart from another known special room — one bad path corrupts the whole solve.
- A blank grid after pressing "Process Data" means at least one input path is inconsistent.

## License

See [LICENSE](LICENSE).
