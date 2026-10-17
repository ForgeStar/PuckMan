# Pac-Man Clone — Scope Lock

**One sentence:** A faithful-ish arcade Pac-Man: steer Pac around a classic 28×31 maze, eat all dots, dodge 4 personality-driven ghosts, survive with 3 lives.

## Core loop
Steer Pac → eat dots/pellets (score) → pellets make ghosts edible (big score) → eat all dots to advance level → get caught, lose a life → game over at 0 lives.

## MVP features
- 28×31 classic-style maze (simplified, no tunnel), 4 power pellets
- Pac-Man: arrow keys / WASD, buffered turns, smooth grid-snapped movement
- 4 ghosts with authentic AI:
  - Blinky (red): chases Pac directly
  - Pinky (pink): targets 4 tiles ahead of Pac
  - Inky (cyan): flanks via Blinky + 2-ahead vector
  - Clyde (orange): chases when >8 tiles away, scatters when close
- Global scatter/chase schedule: 7s, 20s, 7s, 20s, 5s, 20s, 5s, chase forever; ghosts reverse on mode switch
- Frightened mode on power pellet (shrinking timer, blinking near end), eaten ghosts return as eyes to the house and respawn
- Ghost house with staggered release; eyes re-enter via house
- Score (10/50/200-400-800-1600), 3 lives, level progression with speed-up
- Retro canvas rendering + simple WebAudio beeps (waka, pellet, ghost, death)
- States: READY → playing → dying → game over / level clear; P to pause, Enter to (re)start

## Controls
Arrows / WASD move · P pause · Enter start/restart · M mute

## Out of scope
- Sound effects beyond tiny beeps; no music
- Tunnel row, fruit/power-ups, two-player, high-score persistence, mobile/touch
- Pixel-perfect arcade maze (using well-known simplified layout)
- Ghost "no upward turn" quirk, level-1 Blinky scatter delay

## Tech
Single self-contained `index.html` (vanilla JS + canvas, no deps). Playable by opening the file.

## Tasks
- [x] 1. Spec
- [x] 2. Maze data + renderer (walls, dots, HUD)
- [x] 3. Pac movement + input buffering
- [x] 4. Ghost grid movement + house/exiting logic
- [x] 5. Ghost AI (scatter/chase/frightened/eyes) + mode timer
- [x] 6. Scoring, lives, states (ready/dying/game over/level up)
- [x] 7. Sound + polish, verify full playthrough (headless smoke tests: 10/10 pass)

TODO (next sprint): BFS pathfinding for eyes (greedy + no-reverse makes their
return trip a long loop); fruits/power-ups; kill-screen/level cap.
