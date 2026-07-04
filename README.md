# 360 Vision — Soulash 2 Mod

A small [Soulash 2](https://store.steampowered.com/app/2399160/Soulash_2/) mod that gives your
character **permanent 360-degree vision** — you see in every direction around you at all times,
**from birth and no matter which race you pick**. No item, no equipment, no ritual.

## Why

Soulash 2 normally limits your sight to an arc in front of your character. The *Hood of a Thousand
Eyes* grants full all-around vision, but it's an item you have to find and keep equipped. This mod
bakes that ability into your character from the start — the way most other roguelikes let you see
around yourself — and it works for **every playable race**.

## What it does

- Every character **you** create is born with all-around (360°) vision.
- It uses the game's native `full_sight` effect (the same power the Hood of a Thousand Eyes
  provides), delivered through an always-on skill → milestone → passive chain (see below).
- **NPCs are not affected.** Their vision and detection are unchanged, so **sneaking still works**.
- Edits no base-game files → safe to publish on the Steam Workshop or Nexus Mods.

## Install

1. Copy the `human_360` folder into `...\Soulash 2\data\mods\`.
2. Launch the game → **Mods** in the main menu → enable **360 Vision**
   (or add `"human_360"` to the `mods` array in
   `%AppData%\Roaming\WizardsOfTheCode\Soulash2\data\user_settings.json`).
3. **Start a NEW game/world.** Mods are applied per world — an existing world won't pick up the
   change. Pick any race; you'll have 360° vision from the first turn.

## Compatibility

- Built and tested against Soulash 2 **v0.9.24.12**.
- Requires the `core_2` mod (base game content).
- **Self-contained** — modifies no base-game files.

## How it's built

| File | Purpose |
| --- | --- |
| `mod.json` | Mod metadata (requires `core_2`) |
| `passives.json` | The `full_sight` passive that grants 360° vision |
| `skills.json` | A "360 Vision" skill every character has from level 1 (`start_level`) |
| `milestones/second_sight/Awakened_Sight.json` | Grants the passive at skill level 1 |

The player is born with the **360 Vision** skill at level 1, and its level-1 milestone immediately
grants the `full_sight` passive. Because NPC skills are defined explicitly per entity (NPCs never
receive `start_level` skills), NPCs never gain the passive — only the player does, so enemy
detection and stealth are untouched.

## Credits

- Mod by **crisdias**.
- Soulash 2 by Artur Śmiarowski / Wizards of the Code.
