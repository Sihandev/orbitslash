# OrbitSlash — Architecture Notes

This document expands on the high-level overview in the
[README](../README.md) and is aimed at contributors and at the eventual
Godot 4 port.

## Game loop

The loop uses `requestAnimationFrame` and computes `deltaTime` (seconds)
between frames. Every system's `update(dt)` is scaled by `dt`, so movement
and timers are frame-rate independent and run identically at 30, 60, or 144 Hz.

```
loop(now):
    dt = clamp((now - last) / 1000, 0, 0.05)   # clamp guards against tab stalls
    if state == PLAYING:
        update(dt)
    render()
    requestAnimationFrame(loop)
```

`dt` is clamped to a max (50 ms) so that returning to a backgrounded tab does
not teleport entities across the world.

## State machine

`Game.state` is one of:

- `PLAYING` — normal simulation.
- `LEVELUP` — simulation paused, upgrade cards shown.
- `PAUSED` — manual pause.
- `GAMEOVER` — death screen, awaiting restart.

Rendering always runs; only `update` is gated by state. This keeps the last
frame visible behind menus.

## Coordinate spaces

- **World space** — where entities live. Unbounded (infinite grid).
- **Screen space** — pixels on the canvas.

`Camera.worldToScreen(x, y)` and `Camera.screenToWorld(x, y)` convert between
the two. Screen shake is applied as a transient camera offset so it never
affects world simulation.

## Configuration

Tunable gameplay constants live in the `CONFIG` object at the top of the
`<script>` block (player speed, sword stats, spawn rate, XP-per-level, etc.).
This is the single place to balance the game and is the data that will become
Godot `Resource` files during migration.

## Object pooling

`ParticleSystem` reuses particle objects to avoid per-frame allocation and GC
pressure, which is the main cost in a bullet-hell-style loop. Enemies and orbs
use simple array compaction (swap-remove) on death.

## Migration guidance

See the migration table in the [README](../README.md#future-plans-godot-4-migration-map).
The golden rule: **logic and rendering are already decoupled**, so the Godot
port mostly deletes the `Renderer` and attaches scene nodes, reusing the
`CONFIG`/`UpgradeSystem` data verbatim.
