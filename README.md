# salt_flats

A browser-based first-person shooter built with Three.js. Travel between destinations, fight waves of drones, collect equipment and scrap, and improve your weapons. The game and its content are currently implemented in a single HTML file: [`salt-flats-shooter.html`](./salt-flats-shooter.html).

## Run the game

Visit <https://iredmegabyte.github.io/salt_flats/salt-flats-shooter.html>

## Build 

There is no build step or package installation. Open `salt-flats-shooter.html` in a modern browser. The page loads Three.js r128 from a CDN, so an internet connection is needed unless that script is made available locally.

To serve the project locally instead:

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000/salt-flats-shooter.html>.

## Gameplay

Start at Home, a safe hub where you can open the navigation map, inventory, and compendium. Choose an available destination, clear its drone waves, collect drops, then travel through an exit gate or return to the map. Clearing a world's final destination unlocks the next world. Cleared destinations can be revisited.

Areas contain waves of drones. Later waves can include ranged Marshalls and faster, heavier Barons in addition to Flies. Enemies drop scrap, consumables, and gear. Walk over pickups to collect them; remaining pickups are collected when an area ends. If equipment does not fit, it is salvaged for scrap. The first clear of an area also opens a one-time supply cache.

The current destination list is:

- **The Salt Flats:** Landing Strip, Glass Dunes, Crate Yard, Pylon Field, Fuel Depot, Salt Citadel.
- **Ember Reach:** Cinder Gate, Vent Fields, Dead Forge, Ember Core.
- **Earth:** Russia, Chicago, Germany. Earth is a submenu on the solar map.
- **Luna, Venus, Mars:** placeholder destinations.

Earth and the later planetary destinations are placeholders; their area settings currently provide minimal combat layouts.

### Controls

| Action | Default |
| --- | --- |
| Move | W, A, S, D or arrow keys |
| Sprint | Left Shift |
| Jump | Space |
| Fire | Left mouse button |
| Aim down sights | Hold right mouse button |
| Pause | P or Esc |
| Inventory | I |
| Navigation map | M |
| Use nearby station or cache | E |
| Weapon slots 1–3 | 1, 2, 3 |
| Item slots 1–2 | Q, R |
| Compendium | C |

Click the game to capture the mouse for looking around. On touch screens, use the left stick to move, drag on the right side to look, and use the Fire, Aim, Jump, and pause buttons. Quick buttons show the item-slot bindings and open the inventory. Keyboard bindings can be changed in **Options → Controls**.

## Inventory and progression

- You have three weapon slots, equipment slots for armor, one artifact, one companion, two consumable item slots, and a backpack.
- Medkits restore health. Overcharge doubles weapon fire rate for 8 seconds.
- Armor reduces incoming damage; equipment can also provide health and movement bonuses. Companions attack nearby drones or restore health.
- Weapon duplicates stack in the backpack. Select a weapon in the inventory and spend 10 copies of that weapon plus scrap to upgrade it. Upgrades increase damage, and the cost increases at each level. A weapon's level is shared by all copies and persists in the save.
- The Home upgrade bench is currently a placeholder. Weapon upgrades are performed from the inventory.

The in-game **Compendium** describes destinations, combat, drops, weapons, equipment, companions, and stats using the live game data.

## Save data

Progress and settings save automatically in the browser's local storage. Saves are local to the browser and device.

Use **Options → Export / import save** to:

- Download the current save as `salt-flats-save.json`.
- Import a JSON save file. Import replaces the current save after confirmation and reloads the game. Export your current save first if you may want to restore it.

**Options → Reset all save data** clears the current save.

## Content configuration

Gameplay content is defined in `salt-flats-shooter.html`:

- `ITEMS` defines weapons, consumables, armor, artifacts, and companions.
- `WEAPON_ARCHETYPES` defines reusable weapon models, projectiles, default stats, and upgrade defaults.
- `ABILITIES` defines movement abilities.
- `ENEMY_TYPES` defines drone types and their combat behavior.
- `WORLDS` defines destination groups, their maps, unlock order, and area settings.
- `DROP` controls the overall pickup chance and the chance assigned to scrap, consumables, and gear. Each item's `drop` setting controls eligibility and its relative weight within a pool.

### Add or customize an item

An item entry uses a unique key as its ID. A weapon can inherit its appearance, projectile, and default stats from a weapon archetype, then override stats and upgrade settings on the item:

```js
ionLance: {
  name: 'Ion Lance',
  slot: 'weapon',
  model: {
    archetype: 'rifle',
    materials: { accent: 0x9d7cff }
  },
  dmg: 2.4,
  rate: 0.5,
  upgrade: {
    copies: 10,
    scrap: 40,
    maxLevel: 5,
    damagePerLevel: 0.4
  },
  drop: { pool: 'gear', weight: 1 },
  scrap: 16,
  desc: 'A long-range energy weapon.'
}
```

`dmg` is damage per hit; `rate` is the delay between shots in seconds. Omitted weapon stats use the selected archetype's `stats`. Upgrade settings can also be placed in `WEAPON_ARCHETYPES` under `stats.upgrade` to provide defaults to child weapons. Item-level settings override the archetype. `drop.pool` can be `items` or `gear`; `drop.weight` is relative to other entries in that pool. Items without a drop setting do not appear in random drops.

### Add a destination or world

Add an area to a world's `nodes` array with a unique `id`, display `name`, map coordinates (`x`, `y`), neighboring area IDs in `to`, and area settings such as `crates`, `waves`, `speed`, `sky`, `fog`, and `desc`. Set `final: true` on a world's final area. Worlds unlock in array order after the previous world's final area is cleared.

Set `submenu: true` on a world to show it as a single entry on the solar map; selecting it opens its list of nodes. `menuTitle` optionally sets the submenu heading. Earth uses this pattern for its regional destinations.

## Development notes

The project currently has no automated test or build scripts. The main game loop and UI are part of the inline JavaScript in `salt-flats-shooter.html`.
