# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Tetris in vanilla JavaScript + HTML5 Canvas + CSS. No dependencies, no package.json, no build step, no tests, no linter. The README and UI text are in Spanish; keep user-facing strings in Spanish.

## Running

Open `index.html` directly, or serve the directory (e.g. `python3 -m http.server 8000`) and visit `http://localhost:8000`.

## Architecture

All game logic lives in a single classic script, `game.js` (`'use strict'`, no modules), loaded by `index.html` after the DOM. It relies on element IDs in `index.html` (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`), so renaming an ID requires updating both files. The overlay is shown/hidden through the `hidden` CSS class defined in `style.css`.

Key design points:

- **State is module-level `let` globals** (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, ...), reset in `init()`, which is also the restart handler.
- **Board** is a `ROWS×COLS` (20×10) matrix of ints; `0` is empty and `1–7` index into both `COLORS` and `PIECES` (piece type = color index). Pieces are `{type, shape, x, y}` with rotation done by producing a new matrix (`rotateCW`); `tryRotate` applies simple horizontal wall kicks `[0,-1,1,-2,2]`.
- **Game loop**: `requestAnimationFrame` `loop(ts)` accumulates `dropAccum` and does gravity when it exceeds `dropInterval`, then calls `draw()` every frame. Pausing and game over stop the loop with `cancelAnimationFrame(animId)`; unpausing restarts it.
- **Piece lifecycle**: `lockPiece()` = `merge()` → `clearLines()` (updates lines/score/level/`dropInterval`) → `spawn()` (promotes `next`, ends the game if the new piece collides). Gravity in `loop`, `softDrop`, and `hardDrop` all end in `lockPiece()`.
- **Scoring/speed**: `LINE_SCORES` × level; soft drop +1/cell, hard drop +2/cell; level = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)` ms.
- **Input**: single `keydown` listener using `e.code` (arrows, `KeyX` rotate, `Space` hard drop, `KeyP` pause).
- Canvas size is hard-coded in HTML (300×600 board, 120×120 next preview) and must match `COLS*BLOCK` / `ROWS*BLOCK` (`BLOCK = 30`) in `game.js`.
