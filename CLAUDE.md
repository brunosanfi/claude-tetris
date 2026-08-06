# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step — open `index.html` directly in a browser or serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

On Windows: `start index.html`

There are no dependencies, no package manager, and no test suite.

## Architecture

Three files, no modules:

- `index.html` — DOM structure: a 300×600 `<canvas id="board">`, a side panel with score/lines/level/next-piece preview (`<canvas id="next-canvas">`), and an overlay div reused for both pause and game-over states.
- `style.css` — dark/retro theme; no framework.
- `game.js` — all game logic (~300 lines, `'use strict'`, no classes).

### State model (`game.js`)

The board is a `ROWS × COLS` (20×10) matrix where `0` = empty and `1–7` = piece color index. The active piece is `current = { type, shape, x, y }` where `shape` is a 2D matrix. `next` holds the upcoming piece.

Key functions and their roles:

| Function | Role |
|---|---|
| `collide(shape, ox, oy)` | Bounds + overlap check — the single source of truth for movement validity |
| `rotateCW(shape)` | Transpose + reverse rows; pure, returns new matrix |
| `tryRotate()` | Applies wall kicks `[0, ±1, ±2]` before discarding rotation |
| `ghostY()` | Projects `current` straight down; used by both `draw()` and `hardDrop()` |
| `lockPiece()` | `merge → clearLines → spawn` — the piece lifecycle |
| `clearLines()` | Iterates board bottom-up; splices full rows and unshifts empty ones |
| `loop(ts)` | `requestAnimationFrame` loop; accumulates delta time against `dropInterval` |
| `init()` | Full reset; also called on restart |

### Tunable constants (top of `game.js`)

| Constant | Default | Note |
|---|---|---|
| `COLS` / `ROWS` | 10 / 20 | Also update canvas `width`/`height` in `index.html` if changed (`COLS×BLOCK` / `ROWS×BLOCK`) |
| `BLOCK` | 30 | Pixel size of each cell |
| `COLORS` | 7 colors | Index 0 = null (empty) |
| `LINE_SCORES` | `[0,100,300,500,800]` | Multiplied by `level` |
| `dropInterval` (initial) | 1000 ms | Speed formula: `max(100, 1000 − (level−1) × 90)` |
