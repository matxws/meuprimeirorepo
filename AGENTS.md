# Agent Guide

## Project

- This is a Godot 4.7 pixel-art game. Open the repository in Godot and run `F5`; the main scene is the title screen.
- The documented player-facing behavior and scene inventory are in [README.md](README.md).
- Use the repository root as the working directory for Godot commands. There is no separate build step.

## Architecture

- `GameSession` (`scripts/state/game_session.gd`) is the global campaign and persistence service. It owns the active session, checkpoints, friends, jump boosts, companion state, and `user://latest_game.json`.
- `SceneRouter` (`scripts/app/scene_router.gd`) is the global navigation and window service. It owns scene changes, fade transitions, and fullscreen toggling.
- `scenes/levels/main.tscn` is the gameplay composition root. Its level behavior lives in `scripts/levels/main.gd`.
- Reusable `.tscn` scenes are the preferred composition boundary. Keep scene-specific behavior in the script attached to that scene and instance reusable scenes rather than duplicating node trees.
- Characters live under `scenes/characters/` and `scripts/characters/`; gameplay UI is composed from the scenes and scripts under `scenes/ui/` and `scripts/ui/`.

## Scene Contracts

- Preserve stable node names and hierarchy used by scripts, especially `Nilo`, `Bia`, `GameplayUI`, `Checkpoints`, and `LevelGeometry`.
- Preserve `%UniqueName` markers and `@onready` child paths when editing scenes. Update all consumers if a required node is renamed or moved.
- `GameSession` and `SceneRouter` are autoloads declared in [project.godot](project.godot); do not instantiate duplicate global services.
- Nodes that must react while paused use an appropriate pause process mode. Check the existing pause menu and router before changing pause behavior.
- After a scene change, wait for later frames before inspecting or mutating the new scene; scene transitions are asynchronous.

## Persistence

- The save schema is versioned and accepts `res://scenes/levels/main.tscn` and `res://scenes/levels/river/river_level.tscn`. Version 1 saves from the first phase remain compatible; river saves additionally validate `level_progress` and wider position bounds. Treat changes to the schema or level path as compatibility changes.
- New-game state is initialized before the preface, but the existing save is replaced when the new level performs its initial save. Do not delete the save earlier unless the product behavior explicitly changes.
- Saves occur on level entry, friendship changes, checkpoint activation, pause-menu save, and window close during an active session.
- Failed attempts restore the last checkpoint while retaining earned friendships and boosts.
- Keep save writes atomic and preserve the existing validation behavior when changing `GameSession`.

## Tests

Run focused tests first from the repository root:

```sh
godot --headless --fixed-fps 120 --path . --script tests/menu_save_test.gd
godot --headless --fixed-fps 120 --path . --script tests/solo_route_test.gd
godot --headless --fixed-fps 120 --path . --script tests/solo_failure_test.gd
```

For persistence across separate processes:

```sh
godot --headless --fixed-fps 120 --path . --script tests/persistence_process_test.gd -- write
godot --headless --fixed-fps 120 --path . --script tests/persistence_process_test.gd -- read
```

Run the full documented suite when changes cross scene, input, persistence, or companion boundaries:

```sh
for t in conversation_test companion_physics_test companion_routes_test follower_reward_test \
         friendship_gameplay_test menu_save_test solo_route_test solo_failure_test; do
  godot --headless --fixed-fps 120 --path . --script tests/$t.gd
done
```

- Tests are standalone `SceneTree` scripts built on [tests/conversation_test.gd](tests/conversation_test.gd). They use real scenes, async frame/timer waits, injected input, `PASS:` output, and a nonzero exit on failure.
- Tests that instantiate `main.tscn` directly may bypass title and preface lifecycle behavior. Activate `GameSession` before testing gameplay UI or saves.
- Companion restoration depends on the `animals` group, each animal's `animal_id`, its `following` state, and its saved position.

## Change Discipline

- Prefer the smallest change consistent with existing GDScript and scene patterns. Do not reformat unrelated files or edit generated `.import` and `.uid` files by hand.
- Add or update a focused test when changing persistence, scene transitions, checkpoint recovery, companion navigation, or input behavior.
- Validate the narrowest relevant test after an edit, then run the full suite when the change affects shared behavior.
- Keep user-facing text consistent with the project's Portuguese UI and preserve the pixel-art presentation.