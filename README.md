# Human (360) — Soulash 2 Race Mod

A small [Soulash 2](https://store.steampowered.com/app/2399160/Soulash_2/) mod that adds a new
playable race, **Human (360)** — identical to the Human race in every way, but **born with
permanent 360-degree vision**. No item required.

## Why

Soulash 2 normally limits your sight to an arc in front of your character. The *Hood of a Thousand
Eyes* grants full all-around vision, but it's an item you have to find and keep equipped. This mod
bakes that ability into a race, so your character is all-seeing from birth — the way most other
roguelikes let you see around yourself.

## What it does

- Adds a new playable race **Human (360)** (id `human_360`): a clone of the Human race — same
  stats (all +1), ages, name pools, and appearance.
- The race is born with a custom passive, **360-Degree Vision**, which uses the game's native
  `full_sight` effect (`{ "effect": "full_sight", "value": 1 }`). The official modding docs
  describe this effect as *"Allows the player to have a 360-degree vision"* — the same power the
  Hood of a Thousand Eyes provides.
- Bundles its own copy of the human portrait parts, so the race shows the normal human portrait
  **without editing any base-game file**.

## Install

1. Copy the `human_360` folder into `...\Soulash 2\data\mods\`.
2. Launch the game → **Mods** in the main menu → enable **Human 360 Vision**
   (or add `"human_360"` to the `mods` array in
   `%AppData%\WizardsOfTheCode\Soulash2\data\user_settings.json`).
3. Restart the game, start a **New Game**, and choose the **Human (360)** race.

## Compatibility

- Built and tested against Soulash 2 **v0.9.24.12**.
- Requires the `core_2` mod (base game content).
- **Self-contained** — modifies no base-game files, so it is safe to publish on the Steam Workshop
  or Nexus Mods.

## How it's built

| File | Purpose |
| --- | --- |
| `mod.json` | Mod metadata (requires `core_2`) |
| `character.json` | Defines the `human_360` race (Human clone + the passive) |
| `passives.json` | The `360-Degree Vision` passive (`full_sight` effect) |
| `assets.json` | Declares the portrait tilesheet |
| `portraits/portrait_parts.json` | Portrait layer atlas (copied from `core_2`) |
| `portraits/restrictions.json` | Assigns the human portrait parts to `human_360` |
| `assets/gfx/portraits/portrait_parts.png` | Portrait spritesheet (copied from `core_2`) |
| `npc/names/`, `npc/surnames/` | Human name pools |

### Portrait note

Soulash 2 does not natively let a modded race reuse the core portraits by reference — a mod's
`restrictions.json` only works for layers that also exist in that mod's `portrait_parts.json`.
So the portrait atlas + image are bundled in the mod, and `restrictions.json` grants those parts
to `human_360`. If a future game update changes the core portrait system, re-copy `portraits/` and
`assets/gfx/portraits/` from `core_2`.

## Credits

- Mod by **crisdias**.
- Soulash 2 by Artur Śmiarowski / Wizards of the Code.
