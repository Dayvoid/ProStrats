# ProStrats Current Status

This file is where the project stands, and how it got there. Refresh **Now** when a phase or a planning session changes the present. Append a new **History** entry. Do not delete old entries. Direction and future scope live in [ROADMAP.md](ROADMAP.md); settled history stays here.

## Now

- **Date:** 2026-09-29
- **Phase:** Direction is written. Phase 1 (template battlefield) has not started. No gameplay code exists.
- **Engine:** Unity 6000.3.7f1, URP 17.3, from the stock URP template. Input System 1.18 and AI Navigation 2.0.9 are in [Packages/manifest.json](Packages/manifest.json). AI Navigation is unused on purpose until obstacles exist.
- **Scenes:** `Assets/Scenes/SampleScene.unity` is the template scene. `Assets/Scenes/Battle.unity` does not exist yet.
- **Source of truth:** the Unity project at the repo root. `Assets_dst/` and `ProjectSettings_dst/` are leftover copies and are not part of the game.
- **Next step:** a Phase 1 implementation plan (scene, simulation types, input actions, UI Toolkit card, selection outline, EditMode tests). Further questions only where a concrete choice is still open. No gameplay code until that plan is agreed.

## Decisions

Entries are dated. Newer entries do not erase older ones. If a decision is reversed, add a new entry that says so.

### 2026-09-29 — Project direction

- **Simulation-first.** A unit has no health pool. Capabilities are outputs of a part graph. Integrity is a float in `[0, 1]`. Bands are derived: Nominal (≥ 0.75), Compromised (≥ 0.40), Disabled (≥ 0.05), Dismantled (< 0.05). Function comes from a per-part curve of integrity.
- **Authoritative C# simulation, Unity as the view.** Fixed 20 Hz tick in plain C# using `Unity.Mathematics`, covered by EditMode tests, with no `MonoBehaviour` inside the sim. GameObjects, URP, and UI Toolkit display state and submit commands. No DOTS/Entities. No WheelColliders. Burst and compute stay available for later ballistics and the SDF pass. About 200 units do not justify ECS; the part graph and the terrain shader are the hard problems.
- **Single player through Phase 5.** The fixed tick is there so a later deterministic or lockstep sim is possible. No networking work is planned in these phases.
- **Assembly-level parts** on the first archetype, a medium tracked tank. The graph can gain children (for example road wheels under a tread) later without a rewrite. Parts: left tread, right tread, drive motor, turret motor, transmission, optics, rangefinder, fuel tank, ammo store, barrel, pilot, gunner.
- **Mobility model.** Straight speed scales with `min(left tread, right tread)` times drive-motor output times transmission. A disabled side sets straight speed to 0. The live side can still pivot in place.
- **Heat.** Drive and turret motors heat under load and cool toward ambient. The sim exposes `AddHeat` for Phase 2 weapons. Phase 1's debug panel can inject heat.
- **Electrical bus** is derived, not a damageable part. The drive motor feeds it while running, with a short reserve so sensors do not die on the same frame the engine stops.
- **Transmission volume** is a local-space oriented box stored on the archetype for later piercing shots. Phase 1 does not resolve hits against it. Phase 2 explosives do not pierce the shell; they apply shock. Piercing ammo is a later round type.
- **Crew.** Stun blocks a station. Dismantled kills that crewmember and takes the station offline. `CanReman` is false on this tank and reserved for large vessels.
- **Stores.** Ammo dismantled self-detonates that unit only; neighbor damage waits for Phase 2. Fuel dismantled kills drive power and flags a fire; area ignition waits for Phase 2.
- **Phase 1 proves function loss** with a development-only debug conditioner (Editor and Development builds). Attacking, shock, neighbor blasts, and terrain deformation are Phase 2.
- **Phase 1 ground** is a flat kinematic plane and custom differential drive, not NavMesh. Orders go through a path seam so Phase 2 can replace "straight to the point" without rewriting commands.
- **UI** for the command card is UI Toolkit.
- **Scale target** is about 100 units per side on a 7950X3D and RTX 4080 Super.
- **Documents.** [ROADMAP.md](ROADMAP.md) is the path forward and is edited when future scope changes. This file is the history. Completed phases in the roadmap stay as shipped.

## History

### 2026-09-29 — Roadmap written

The repo was a stock Unity 6.3 URP template: Input System and AI Navigation installed, no gameplay scripts, plus unused `Assets_dst/` and `ProjectSettings_dst/` trees.

[ROADMAP.md](ROADMAP.md) now records the simulation-first architecture, the medium-tank part graph, and Phases 1–5 with in/out scope and exit criteria. Phase 1 is not started. The next session plans Phase 1 implementation only.
