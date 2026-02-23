# data/moves/

This directory contains two distinct resources that together define the complete move system for VPG Engine.

---

## Files

### `move-slots.md`
A human-readable reference defining every **move slot** in the game — the named input combinations available to every wrestler, organized by position and context (standing, grappling, ground, turnbuckle, etc.).

This is the authoritative reference for understanding the slot system. Read this to understand:
- What move slots exist and how they are named
- What input triggers each slot
- What opponent state and setup is required
- How many slots exist per position/grapple type

This file is **not** generated from any other file. If `move-slots.md` and `move-database.json` ever conflict on slot naming, `move-slots.md` is the authority.

**Slot counts at a glance:**

| Position | Grapple Type | Slots |
|---|---|---|
| Front | Weak Grapple | 8 |
| Front | Strong Grapple | 8 + 1 Finisher Slot |
| Back | Weak Grapple | 4 |
| Back | Strong Grapple | 4 + 1 Finisher Slot |

> For full slot counts across all categories (Ground, Turnbuckle, Ringside, Apron, Irish Whip, Taunt, Double-Team) see `move-slots.md`.

---

### `move-database.json`
The programmatic master list of every **move** available in the game. This is the file the engine reads. Each entry defines a move's properties — not its slot assignment, which is handled per-character in `assets/characters/{character}/moves.json`.

Each move entry contains:

| Property | Type | Description |
|---|---|---|
| `id` | string | Unique move identifier |
| `name` | string | Display name |
| `power` | string | Damage tier: `F` → `E` → `D` → `C` → `B` → `A` → `S` (weakest to strongest), `G` for special gag moves |
| `ko` | boolean | Whether this move can cause a knockout |
| `bleed` | boolean | Whether this move can open a blade job |
| `feature` | string | Special outcome: `Pin`, `Submit`, or `null` |
| `eligible_slots` | array | Which slot categories this move can be assigned to |

> **Status**: `move-database.json` is currently in progress. The source data exists in `move-slots.md` in table format and is being migrated to JSON.

---

## How These Files Relate

```
move-slots.md          defines what slots exist and what inputs trigger them
      ↓
move-database.json     defines what moves exist and which slots they are eligible for
      ↓
assets/characters/{character}/moves.json    assigns a move to each slot for a specific character
```

A move cannot be assigned to a slot it is not eligible for. Eligible slots are defined per move in `move-database.json`.

---

## Related

- `data/schemas/move-slots.json` — JSON schema defining the structure of the slot system
- `assets/characters/` — Per-character move assignments
- `docs/mechanics/GRAPPLING-SYSTEM.md` — Explanation of how the grappling system works
- `CONTRIBUTING.md` — How to add new moves or document move properties
