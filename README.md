# OrbitSlash

> A fast, minimalist arena survival game. You are a blade in orbit — circle, slash, survive.

OrbitSlash is a single-file, zero-dependency HTML5 prototype built on the Canvas
API. A blue orbiter spins a sword around itself, carving through endless waves of
enemies while collecting XP and leveling up with roguelite upgrades. The
prototype is intentionally small and clean so it can be migrated to **Godot 4**
with minimal friction.

---

## Project Overview

OrbitSlash takes the "auto-attacking survivor" loop (à la *Vampire Survivors*)
and strips it to its core fantasy: a single rotating blade. There are no menus
to fight through, no asset pipeline, and no build step — open `index.html` and
play. Every system is implemented as a small, single-responsibility ES6 class,
mirroring the node/scene decomposition you would use in Godot 4.

**Design pillars**

- **Instant play** — one HTML file, no libraries, no frameworks, no build.
- **Readable architecture** — one class, one responsibility.
- **Engine-portable** — structure maps cleanly onto Godot 4 nodes and scenes.
- **Game-feel first** — particles, screen shake, hit flash, and trails over
  feature count.

---

## Features

- 🔵 **Orbiting blade combat** — a configurable sword (speed, length, damage,
  rotation) sweeps around the player and destroys enemies on contact.
- 🕹️ **Four control schemes** — WASD, arrow keys, mouse-follow, and an
  on-screen virtual joystick for touch.
- 🎥 **Smooth follow camera** over an **infinite scrolling grid**.
- 👾 **Endless enemy waves** that spawn off-screen and home in on the player.
- ✨ **XP orbs** that drop on kill and are auto-collected.
- 📈 **Level-up system** — every 10 XP the game pauses and offers **3 random
  upgrades**.
- 💥 **Juice** — death particles, floating XP text, sword trail, hit flash, and
  screen shake.
- 🖥️ **Responsive & 60 FPS** — delta-time game loop, resizes to any viewport.

---

## Controls

| Action          | Input                                             |
| --------------- | ------------------------------------------------- |
| Move            | `W` `A` `S` `D` / Arrow Keys                       |
| Move (mouse)    | Hold **Left Mouse Button** — player moves to cursor |
| Move (touch)    | **Virtual joystick** (drag anywhere on screen)    |
| Choose upgrade  | Click an upgrade card, or press `1` / `2` / `3`   |
| Pause / Resume  | `P` or `Esc`                                       |
| Restart (on death) | `Enter` or click **Restart**                   |

---

## Getting Started

No install, no build:

```bash
git clone https://github.com/Sihandev/orbitslash.git
cd orbitslash
# Open the prototype in any modern browser:
#   prototype/index.html
```

Or serve it locally (recommended so input focus behaves):

```bash
cd prototype
python -m http.server 8000
# then visit http://localhost:8000
```

---

## Roadmap

**Sprint 1 — Core loop (this prototype) ✅**

- Player movement, orbiting sword, enemy spawning, XP, level-up, juice.

**Sprint 2 — Combat depth**

- Multiple enemy types (fast, tank, ranged).
- Multiple weapon types and a real weapon-slot system.
- HP/damage balancing pass and difficulty curve over time.

**Sprint 3 — Progression**

- Persistent coins and a meta-upgrade shop.
- Boss waves and a win/loss summary screen.
- Audio: SFX and adaptive music.

**Sprint 4 — Godot 4 migration**

- Port each class to a Godot node/scene (see *Future Plans*).
- Replace canvas draw calls with `Node2D` / `Sprite2D` rendering.
- Reuse the data-driven upgrade and spawn tables as Godot Resources.

---

## Architecture

The prototype is a single `index.html` containing one `<canvas>` and a set of
ES6 classes. The game loop is **delta-time driven** so behavior is
frame-rate independent.

```
Game            Orchestrator: owns the loop, state, and all systems.
├─ Input        Keyboard, mouse, and virtual-joystick state.
├─ Camera       Follows the player; converts world ↔ screen space.
├─ Renderer     All canvas drawing (grid, entities, effects).
├─ Player       Blue circle; position, health, movement.
├─ Sword        Rotating rectangle; configurable speed/length/damage.
├─ Enemy        Homing entity with health and hit flash.
├─ EnemySpawner Spawns enemies off-screen on a timer.
├─ XPOrb        Collectible XP pickup with magnet behavior.
├─ Collision    Stateless circle/rect intersection helpers.
├─ UpgradeSystem Defines upgrades and applies them on level-up.
├─ ParticleSystem Pooled particles + floating text.
└─ UI           HUD (HP/XP/Level/FPS/Kills/Coins) + menus.
```

**Why this shape?** Each class has exactly one responsibility and communicates
through the `Game` orchestrator, which keeps coupling low and makes each unit
trivial to lift into another engine.

---

## Future Plans (Godot 4 migration map)

The class boundaries were chosen to map 1:1 onto Godot concepts:

| Prototype class   | Godot 4 equivalent                                  |
| ----------------- | --------------------------------------------------- |
| `Game`            | Main scene + autoload `GameManager`                 |
| `Player`          | `CharacterBody2D` scene                             |
| `Sword`           | `Node2D` child of Player + `Area2D` hitbox          |
| `Enemy`           | `CharacterBody2D` scene with `Area2D`               |
| `EnemySpawner`    | `Node` with a `Timer`                               |
| `XPOrb`           | `Area2D` scene                                      |
| `Camera`          | `Camera2D` (built-in smoothing)                     |
| `Renderer`        | Native scene-tree rendering (removed)               |
| `Input`           | Godot `InputMap` actions                            |
| `Collision`       | Built-in `Area2D` / physics layers                  |
| `ParticleSystem`  | `GPUParticles2D` / `CPUParticles2D`                 |
| `UpgradeSystem`   | `Resource`-based upgrade definitions                |
| `UI`              | `CanvasLayer` + `Control` nodes                     |

Because logic and rendering are already separated, the migration is mostly
"delete the `Renderer`, attach scenes, and reuse the data."

---

## License

[MIT](LICENSE) © 2026 Sihandev
