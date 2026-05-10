# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Language

始终使用中文回复用户。

## Repository overview

A single-file vanilla web countdown timer — no frameworks, build tools, or dependencies. Open `timer.html` directly in a browser to use. Theme preferences persist via `localStorage`.

## Git remote

Remote is `git@github.com:hbczchx/Mygit-test1.git`, branch `master`. Use SSH authentication (key at `~/.ssh/id_ed25519`).

## Architecture: timer.html

All CSS and JS are inline in one file. The timer uses a simple state machine with three states managed by two variables:

- `timerId` — `null` when idle/finished, a `setInterval` handle when running
- `isPaused` — `true` only when paused mid-countdown

**Color system** — Every color is a CSS custom property defined on `:root` (fallback) and overridden by `[data-theme="blue|green|orange"]` blocks. When adding new UI elements, always use `var(--something)` rather than hardcoded colors. Add the variable to each of the 4 blocks (`:root` + 3 theme blocks).

**Progress ring** — An SVG `<circle>` whose `stroke-dashoffset` is JS-controlled relative to `remaining / totalSeconds`. The circle's circumference is `2π × 130 ≈ 816.8`. The ring turns red (`.finishing`) for the last 10 seconds.

**Audio** — Uses the Web Audio API `OscillatorNode` for the alarm beep (two-tone 880→660 Hz). Wrapped in try/catch because `AudioContext` requires a user gesture on some browsers.

**Key JS functions:**
- `updateDisplay()` — syncs the text display and ring offset to `remaining`
- `readInput()` / `setFromInput()` — read/write the H/M/S input fields
- `setPreset(minutes)` — applies a preset button's value
- `tick()` — the 1 Hz callback, decrements `remaining`, calls `finish()` at zero
- `finish()` — clears interval, shows alarm toast, plays beep, resets buttons

**Button visibility** — Start and Pause share the same slot (one hidden via `display:none`). The Start button text changes to "继续" after pause and back to "开始" after reset/finish.
