# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **data-only mod for the game Soulash 2**. There is no build, compile, lint, or test toolchain —
the "code" is JSON data files that the game loads at startup and merges on top of its base mod,
`core_2`. The mod gives **every character the player creates permanent 360-degree vision from birth,
regardless of race**, without editing any base-game file and without affecting NPCs.

`human_360/` is the mod itself (the shippable artifact). The repo root is the source of truth; the
game loads a *copy* placed inside its own folder (see deploy loop below), so two copies exist and
must be kept in sync.

> History: this started as a cloned "Human (360)" race, then a transformation ritual/elixir, before
> landing on the current skill-based approach. The race, its portrait bundle, and the elixir were
> removed — see `git log`. Ignore any lingering references to them.

## Deploy & test loop

There are no unit tests. Verification is: deploy → restart → **generate a NEW world** → read the log
and look around in-game.

```bash
GAME="/c/Program Files (x86)/Steam/steamapps/common/Soulash 2"

# 1. Validate every JSON parses (do this before deploying)
find human_360 -name '*.json' -print -exec python -c "import json,sys; json.load(open(sys.argv[1],encoding='utf-8'))" {} \;

# 2. Deploy the mod into the game
cp -r human_360 "$GAME/data/mods/"

# 3. Confirm the game loaded it without errors (after launching)
cat "$GAME/logs/errors.log"     # should list "- human_360" and no parse/reference errors
```

- **Activate the mod**: add `"human_360"` to the `mods` array in
  `%AppData%\Roaming\WizardsOfTheCode\Soulash2\data\user_settings.json` (or via the in-game Mods menu).
- **Mods apply per world.** Toggling or installing a mod only affects **new** worlds — you must
  generate a new world to see changes; restarting alone is not enough.
- **Cheat console** (for testing): set `"debug": { "enabled": true }` in `user_settings.json` **while
  the game is closed** (it is read at startup, and the game rewrites the file on exit). In-game
  (with a save loaded) open it with the backtick key. Useful: `exp <n> <skill_id>`,
  `skill_p <n> <skill_id>`, `spawn <id> [count]`.
- **Manual test**: New Game → new world → pick any race → confirm 360° vision from the first turn.
- The authoritative modding reference ships at `<install>/data/docs/index.html` (skills = §5,
  passive-effects table ~lines 1400-2100, cheat console = §17.1). `full_sight` = 360° vision.

## Architecture

The mod is an **additive data overlay on `core_2`** (declared via `mods_required` in `mod.json`). It
never edits base-game files. The 360° vision is delivered by a three-link, **player-only** chain:

- **`passives.json`** — a passive whose effect is the native `{ "effect": "full_sight", "value": 1 }`
  (documented as "360-degree vision"). A passive only applies to whoever references it; it is not
  global.
- **`skills.json`** — a `"360 Vision"` skill (id `human_360_second_sight`) with **`start_level: 1`**.
  `start_level` is undocumented but makes the *player* start with the skill at that level. It has no
  `exp_sources`, so it is innate and never levels.
- **`milestones/second_sight/Awakened_Sight.json`** — a milestone with `requirements.skill: 1` whose
  reward is `{ "passive": "human_360_full_sight" }`. The player reaches level 1 at birth (via
  `start_level`), which grants the passive → 360° vision from the first turn.

### Why NPCs are unaffected (the non-obvious part)

`start_level` only affects the **player's** starting skills. **NPC skills are enumerated explicitly
per entity** in each actor's `skills` component (e.g. a Human Guard lists only `athletics` +
`pole_fighting`; NPCs don't get `alchemy` despite it having `start_level: 1`). So NPCs never receive
the `"360 Vision"` skill or its passive. NPC detection uses each NPC entity's own `sight` component
(`range` / `threesixty: false`), which this mod never touches — so **stealth is unaffected**. Keep
the effect player-only; do not move it onto a race, entity, or anything NPCs inherit.

## Working rules

- **Never edit `core_2` or any base-game file.** Keep everything inside `human_360/` so the mod
  stays portable and publishable (Nexus / Steam Workshop) and survives game updates. Use `core_2`
  only as a source to read from.
- Keep `mod.json` `game_required` aligned with the tested game version (currently `0.9.24.12`).
- Remember mods apply **per world** — always test in a freshly generated world.
