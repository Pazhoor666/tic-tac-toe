# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A single-file browser tic tac toe game with a graph-paper look. Everything (markup, CSS, JS) lives in `tic-tac-toe.html`. There is no build step, package manager, linter or test suite.

## Running

Open `tic-tac-toe.html` directly in a browser (e.g. `start tic-tac-toe.html` on Windows). No server needed. The only external dependency is the Google Fonts stylesheet (Kalam, IBM Plex Mono), with system font fallbacks.

The file has no `<!doctype>`/`<html>`/`<head>`/`<body>` wrappers. It starts with `<title>`, then `<style>`, markup, and a `<script>` IIFE at the end. Browsers handle this fine; keep the fragment shape unless asked to change it.

## Architecture (inside the `<script>` IIFE)

- **State**: module-level `board` (9-element array of `'X' | 'O' | null`), `turn`, `over`, `starter`, `mode` (`'cpu'` | `'2p'`), `diff` (`'easy'` | `'hard'`), and `score` `{X, O, D}`.
- **Flow**: `humanMove(i)` guards input, then `place(i)` writes the move, checks `winner()`, flips `turn`, calls `render()`, and in CPU mode schedules the computer's move with `setTimeout`. `finish(w)` handles game end: score, animated strike-through line, dimming, locking.
- **`winner(b)`** returns `{p, line}` for a win, `{p: 'D'}` for a draw (`D` is also the score key for draws), or `null`.
- **AI**: the human is always X and the computer is always O. `cpuPick()` plays randomly 60% of the time on Easy, picks a random corner/center on an empty board, and otherwise uses `minimax(board, 'O')`. Minimax scores ±1 and moves scores toward 0 by depth so it prefers faster wins and slower losses.
- **`render(w)`** is the single place that updates status text, player labels and the tally scoreboard. Call it after state changes instead of touching those DOM nodes directly.
- **`newRound()`** resets the board and switches `starter` so the opening player alternates. `resetScore()` zeroes the score and is called on every mode switch.
- **Input lock**: `lock(on)` disables cells while the computer is "thinking". Occupied cells and finished games stay disabled either way.
- **Keyboard**: keys `1`–`9` map to squares (top-left to bottom-right). `n`, or `Enter` after a game ends, starts a new round.

## Styling conventions

- Colors are CSS custom properties on `:root`. Dark mode overrides them under `@media (prefers-color-scheme: dark)` with `:root:not([data-theme="light"])`, and again under `:root[data-theme="dark"]`. When you add a color, define it in all three places.
- X/O marks are inline SVG built by `markSvg()`. The win strike is an SVG path animated with `stroke-dashoffset`, in a 300×300 board coordinate space with 100px cells.
- Layout must work at phone width (16px side gutter, max column width 440px).

## Workflow

Commit each change and push to `Pazhoor666/tic-tac-toe` on GitHub (`main`).
