# Pac-Man (Bionic Clone)

A single-file, dependency-free Pac-Man clone built with vanilla JS + canvas.

## How to run
Open `index.html` in any browser (double-click it, or drag it into a tab). No build step, no server.

## Controls
| Key | Action |
| --- | --- |
| Arrow keys / WASD | Move |
| P | Pause / resume |
| M | Mute / unmute |
| Enter or Space | Start / restart (after game over) |

## Gameplay
- Eat all 260 dots to clear the level. Dots = 10 pts, power pellets = 50 pts.
- Power pellets turn ghosts blue and edible: 200 → 400 → 800 → 1600 pts per ghost.
- Eaten ghosts return home as eyes and re-enter the fray.
- 3 lives. Levels get progressively faster.

## The ghost gang (classic arcade AI)
- **Blinky** (red) — chases your tile directly.
- **Pinky** (pink) — targets 4 tiles ahead of you (ambush).
- **Inky** (cyan) — flanks using Blinky's position + a 2-tile-ahead vector (pincer).
- **Clyde** (orange) — chases when far, retreats to his corner when close.

Ghosts alternate scatter (7s) ↔ chase (20s) with reversals on each switch,
just like the 1980 arcade original.

## Files
- `index.html` — the entire game
- `SPEC.md` — scope-lock spec + task list
