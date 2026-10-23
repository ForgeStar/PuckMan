# PuckMan — Audio / BGM Notes

Single-file game: `PuckMan.html` (+ `bezel.png`). No external deps, must work
offline via `file://`. All sound is **pure Web Audio synthesis** (oscillators +
LFOs), no samples, so nothing to load.

## The big insight (why earlier attempts sounded wrong)
The original Pac-Man "continuous WooWooWoo" is **NOT a melody and NOT the waka**.
It is a separate **continuous background "mid-siren"**: a low triangle tone whose
pitch **wobbles up and down via an LFO** — which is exactly why it sounds like a
police siren (and that's correct). The **waka-waka chomp rides ON TOP** of it.
Eating a power pellet swaps the siren for a higher **frightened alarm** while
ghosts flash blue. My earlier mistake was putting the siren *into* the waka.

## What's implemented in `PuckMan.html` (audio section, after the `sfx` object)
- `bgmNodes` / `bgmKey` — state for the running ambient layer.
- `stopBGM()` — stops + disconnects the current ambient layer.
- `startSiren(rate, center, depth, vol)` — triangle osc, LFO (triangle) on
  frequency. This is the WooWooWoo background.
- `startFright()` — sawtooth ~1080 Hz, square LFO 9 Hz ±300 Hz (warble
  ~780–1380 Hz), the eerie frightened-mode tone.
- `SIREN_TIERS[4]` — (rate, center, depth). Rates were ~2×'d after user said the
  woo-woo was slower than the original:
  - tier0: 2.2 Hz, 310 Hz, ±70  (full maze)
  - tier1: 2.6 Hz, 325 Hz, ±78
  - tier2: 3.2 Hz, 350 Hz, ±86
  - tier3: 4.0 Hz, 390 Hz, ±96  (fewest dots — fastest/loudest)
  Siren volume: 0.05. Fright volume: 0.05.
- `sirenTier(frac)` — frac = dotsLeft/dotsTotal. >0.70→0, >0.45→1, >0.25→2, else 3.
- `INTRO_NOTES` — B-major level-start jingle, transcribed from the original ROM
  data (via Google's pacman doodle engine). Played by `playIntro()` on the first
  start key (a user gesture, so audio is unlocked). ~3.9s, sine, vol 0.06.
- `bgmUpdate()` — called every frame (in `frame()`, next to `wakaTick()`). Only
  runs an ambient layer during `state === "play"`. Fright if `frightTimer > 0`,
  else siren at the current tier. Stops on mute / any non-play state.
- `dotsTotal` — added to entity vars, set in `newGame()` and on level-up.
- Waka peak gain is `WAKA_VOL` = 0.05 (user-tuned; matches the 0.05 siren).

### Waka (Namco WSG continuous sweep — ACCEPTED by user)
FINAL tuned values (user-set): `WAKA_INTERVAL = 0.13` (line 251, chomp speed),
`WAKA_LO`/`WAKA_HI = 196/220` (line 252, G3/A3 pitch), `WAKA_VOL = 0.05` (line 253),
chomp tail `wakaUntil = now + 0.3` (line ~283). `wakaNote(t, f1, f2, dur)` is a **square
sweep** (fast attack, sustain to 0.75, quick release); `wakaTick` alternates up/down
back-to-back. Re-firing train while eating.
History: 4-note @ 35 ms ("very fast") → 257↔289 glide @ 67 ms ("even faster"; continuous
reads fast) → discrete static blips → Grok's continuous sweep (authentic) → user tuned
speed/pitch/vol to taste. **Done — user confirmed it looks and sounds great.**
Tuning ref: `WAKA_INTERVAL` (speed), `WAKA_LO`/`WAKA_HI` (pitch, whole-step apart),
`WAKA_VOL` (vol), `wakaUntil` tail (~283). Below ~150 Hz a square wave gets weak — swap to
triangle/pulse if going that deep.

## Reference resources (user-provided, verified)
- **Google Pac-Man doodle audio engine** (pure WSG-style synthesis, has every
  track incl. siren tiers, fright, intro):
  `https://raw.githubusercontent.com/livingston/google-pacman/master/pacman-audio.js`
  (saved to `C:\tmp\pacman-audio.js`). Its `makeSiren(rate,center,depth)` =
  triangle + triangle LFO; `makeFright()` = sawtooth 120 Hz + 7.5 Hz FM;
  `makeEyes()` = square 550 Hz pulsed 16.67 Hz; `start-music` = B-major melody.
- **WSG hardware:** Namco 3-voice wavetable, 96 kHz clock (Z80 3.072 MHz ÷ 32),
  8 waveforms (32×4-bit samples) in two PROMs, freq/vol/wave regs updated ~60 Hz.
- **Chris Lomont Pac-Man Emulation Guide (PDF):**
  `https://www.lomont.org/software/games/pacman/PacmanEmulation.pdf`
- **vecoven disassembly + sound tables:** `http://www.vecoven.com/elec/pacman/pacman.html`
- **MAME `namco.cpp`** — accurate software WSG model.
- Reference gameplay clip: `https://www.youtube.com/watch?v=i_OjztdQ8iw`

## Prior DSP extraction (from the downloaded reference clip, `C:\tmp\pacmanref.wav`)
- In-game BGM loop period ≈ 1.146 s. Pitch-tracked (approx, waka contamination
  possible): `F5 G5 A5 F5 D#5 A4 D5 E5 G#5 A#5 G#5 F#5 E5 D5 A#4 D5 E5 F#5 G#5
  A#5 G#5 F#5 E5 A#4 G#4` (recognizable ascending/descending chase figures).
  NOTE: we now lean on the *documented* siren (LFO wobble) rather than this
  melody for the background, since the real BGM is a siren, not a note sequence.
- Frightened-section high-pitch windows (from per-second scan): t≈20–22s,
  54–58s (~1270–1375 Hz), 66–72s (~1140–1320 Hz), 88–90s (~1225 Hz).
  → informed the high (~1080 Hz) frightened alarm choice.

## Tuning knobs (if user wants changes)
- Siren too loud/soft → `startSiren(..., vol)` in `bgmUpdate()` (currently 0.05).
- Siren too slow/fast → `SIREN_TIERS` rate/center/depth.
- Fright pitch → `startFright()` (1080 / LFO 9 Hz / ±300).
- Intro too long → trim `INTRO_NOTES`.
- Waka speed/pitch/vol → `WAKA_INTERVAL` (0.13 s), `WAKA_LO`/`WAKA_HI` (196/220), `WAKA_VOL` (0.05); chomp tail `wakaUntil` (~0.3).
- Waka vs siren balance → `WAKA_VOL` (0.05) vs siren vol (0.05).

## Open / unresolved
- **BGM + waka: DONE — accepted by user** ("looks and sounds great"). If revisited, the
  siren (SIREN_TIERS), frightened alarm (startFright), and intro (INTRO_NOTES) are the
  other tunable layers; all documented above.
- Delete-or-keep the 3 original bezel files (`.eps` 6.7 MB, `.png` 2.4 MB,
  `.jpg` 0.7 MB) — user never answered. `bezel.png` is the one actually used.

## Dev/verification
- JS syntax check: extract inline script → `deno check`
  (`C:\Users\rex\AppData\Local\Programs\Python\Python311\Scripts\deno.exe`).
- Headless test (audio autoplay warnings expected): Brave `--headless=new`
  `--screenshot` `--window-size=1000,950 --virtual-time-budget=3000 file://...`
