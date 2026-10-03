# salt_flats
Experimental 3JS shooter.

## Data-driven items and weapons

Items and weapon archetypes are defined in `salt-flats-shooter.html` in the `ITEMS` and `WEAPON_ARCHETYPES` objects. A weapon can inherit its model, projectile, base stats, and upgrade defaults from `model.archetype`, then override individual properties. Set `drop.pool` to `items` or `gear` to make an item eligible for that loot pool; `drop.weight` controls its relative chance within the pool. Weapon `upgrade` settings control duplicate copies and scrap required, the maximum level, and damage gained per level. Weapon levels are saved per weapon ID, so every equipped copy of the same weapon shares its level.

For example, a new weapon can reuse the rifle model and stats while customizing its own progression:

```js
ionLance: {
  name: 'Ion Lance',
  slot: 'weapon',
  model: { archetype: 'rifle', materials: { accent: 0x9d7cff } },
  dmg: 2.4,
  rate: 0.5,
  upgrade: { copies: 10, scrap: 40, maxLevel: 5, damagePerLevel: 0.4 },
  drop: { pool: 'gear', weight: 1 },
  scrap: 16,
  desc: 'A long-range energy weapon.'
}
```
