# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **data-only mod for the game Soulash 2**. There is no build, compile, lint, or test toolchain —
the "code" is JSON data files plus PNG assets that the game loads at startup and merges on top of
its base mod, `core_2`. The mod adds one playable race, **Human (360)**, that is a clone of the
Human race but born with permanent 360-degree vision.

`human_360/` is the mod itself (the shippable artifact). The repo root is the source of truth;
the game loads a *copy* placed inside its own folder (see deploy loop below), so two copies exist
and must be kept in sync.

## Deploy & test loop

There are no unit tests. Verification is: deploy → restart game → load a character → read the log.

```bash
GAME="/c/Program Files (x86)/Steam/steamapps/common/Soulash 2"

# 1. Deploy the mod into the game
cp -r human_360 "$GAME/data/mods/"

# 2. Validate every JSON parses (do this before deploying)
find human_360 -name '*.json' -print -exec python -c "import json,sys; json.load(open(sys.argv[1],encoding='utf-8'))" {} \;

# 3. Confirm the game loaded it without errors (after launching)
cat "$GAME/logs/errors.log"     # should list "- human_360" and no parse/reference errors
```

- **Activate the mod**: add `"human_360"` to the `mods` array in
  `%AppData%\WizardsOfTheCode\Soulash2\data\user_settings.json` (or via the in-game Mods menu).
- **A full game restart is required** after changing assets/tilesheets — returning to the menu is
  not enough; new images are only loaded at startup.
- **Manual test**: New Game → character creation → pick the **Human (360)** race. Check the
  portrait renders (human appearance) and, in-game, that vision extends in all directions.
- The authoritative modding reference ships with the game at `<install>/data/docs/index.html`
  (races = §7, portraits = §8, passive effects table lists `full_sight`, `sight_behind`, etc.).

## Architecture

The mod is a **data overlay on `core_2`** (declared via `mods_required` in `mod.json`). It never
edits base-game files; it only adds/duplicates. A playable race is assembled across several files:

- **`character.json`** — defines the `human_360` race under a `races[]` array: a field-for-field
  clone of the core Human race (id `"0"`) with a new string id `"human_360"`, plus a `passives`
  array. Race ids are **strings**, not numbers.
- **`passives.json`** — the `360-Degree Vision` passive. 360 vision is the native engine effect
  `{ "effect": "full_sight", "value": 1 }`. The race gains it purely by listing the passive id in
  its `passives` array — the same mechanism the core Dwarf uses for `core_2_infravision`.
- **Portrait bundle** (`assets.json` + `portraits/portrait_parts.json` + `portraits/restrictions.json`
  + `assets/gfx/portraits/portrait_parts.png`) — see the constraint below.
- **`npc/names/`, `npc/surnames/`** — name pools the race references from `character.json`.

### The portrait constraint (the non-obvious part)

Soulash 2 does **not** let a modded race reuse the core portraits by reference. A mod's
`restrictions.json` (which maps portrait layer → race id) only takes effect for layers that also
exist in that **same mod's** `portrait_parts.json` atlas, whose frames point into an image declared
in that mod's `assets.json`. Granting core layers to a new race from a mod-only `restrictions.json`
silently renders an empty portrait.

So the portrait files here are **copies of the `core_2` portrait atlas + spritesheet**, bundled into
the mod, with `restrictions.json` re-granting the human portrait parts to `human_360`. The three
files link by name: a `restrictions.json` key is a layer name → it must exist in
`portrait_parts.json` `meta.layers` (with a `group`) and have a frame `generated_portraits (<name>).aseprite`
whose coordinates index the PNG. If the game's portrait system changes in an update, re-copy
`portraits/` and `assets/gfx/portraits/` from `core_2`.

## Working rules

- **Never edit `core_2` or any base-game file.** Keep everything inside `human_360/` so the mod
  stays portable and publishable (Nexus / Steam Workshop) and survives game updates. Use `core_2`
  only as a source to copy from.
- Keep `mod.json` `game_required` aligned with the tested game version (currently `0.9.24.12`).
