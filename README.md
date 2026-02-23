# VPG Engine (Virtual Pro Grappler)

A fully free, open-source professional wrestling video game engine built on modern web technology, designed to recreate the legendary gameplay mechanics and environmental design of WWF No Mercy.

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

---

## 🎯 Project Vision

VPG Engine aims to faithfully recreate the core gameplay experience of WWF No Mercy while being:

- **Completely free and open-source** (GNU GPL 3.0)
- **Built on modern web technology** (JavaScript/Web-based)
- **Legally clean** — zero copyrighted content in the final release
- **Highly customizable** — JSON-driven content with full modding support
- Built with [Babylon.js](https://www.babylonjs.com/)

The engine is being developed using No Mercy as a design reference, with all final assets being original creations. Characters are built in [Blockbench](https://www.blockbench.net/) and exported as GLB files with per-part texture mapping.

### What We're Recreating

- **Grappling system** — The iconic position-based grappling mechanics
- **Strike system and timing** — Timing and counter-strike systems
- **Momentum (Attitude) meter** — The risk/reward special move system
- **Damage scaling** — Body part damage and move effectiveness
- **Reversal windows** — Frame-perfect timing and reversals
- **Ground game** — Submission, pin, and ground attack systems
- **Movement & physics** — Running, ropes, turnbuckles, and environmental interaction
- **Environmental accuracy** — Ring dimensions, collision points, and object behavior

---

## Project Overview

The core design principles are:

- **N64-era aesthetics** — low-poly blocky character models, baked texture shading, hard edges
- **No Mercy-faithful controls** — weak/strong grapple system, directional move modifiers, special meter, finishers
- **Data-driven characters** — every wrestler is defined entirely by JSON files, making it easy to add new characters, attires, and move sets without touching engine code
- **Texture compatibility** — the texture system is designed so that PNG files ripped from the original No Mercy ROM (via emulator tools) can be dropped into a character folder and mapped to the model with a manifest file

---

## Folder Structure

```
vpg-engine/
│
├── public/                          # Static assets served directly
│   ├── index.html
│   └── favicon.ico
│
├── src/                             # All game source code
│   ├── main.js                      # Entry point, initializes Babylon.js scene
│   ├── engine/                      # Core game systems
│   │   ├── SceneManager.js          # Ring, camera, lighting setup
│   │   ├── InputManager.js          # Controller/keyboard input handling
│   │   ├── PhysicsManager.js        # Babylon.js physics setup
│   │   └── MatchManager.js          # Match state, win conditions, referee logic
│   │
│   ├── characters/                  # Character logic
│   │   ├── Character.js             # Base character class
│   │   ├── CharacterLoader.js       # Loads GLB + applies texture manifest
│   │   ├── CharacterController.js   # Movement, state machine (standing/grappled/downed)
│   │   └── AnimationController.js   # Blends and triggers animations
│   │
│   ├── moves/                       # Move system
│   │   ├── MoveEngine.js            # Reads move slots, executes moves
│   │   ├── GrappleSystem.js         # Grapple initiation, weak/strong logic
│   │   ├── CollisionDetector.js     # Hitbox checks for strikes and grapples
│   │   └── FinisherSystem.js        # Special meter, finisher triggers
│   │
│   └── ui/                          # HUD and menus
│       ├── HUD.js                   # Health bars, special meter, timer
│       ├── MainMenu.js
│       └── CharacterSelect.js
│
├── assets/                          # All game assets
│   ├── characters/                  # One folder per wrestler
│   │   └── the_rock/
│   │       ├── model.glb            # Blockbench export
│   │       ├── textures/
│   │       │   ├── base/            # Skin/body base textures
│   │       │   ├── attire_1/
│   │       │   ├── attire_2/
│   │       │   ├── attire_3/
│   │       │   └── attire_4/
│   │       ├── attire_1.json        # Texture manifest for attire 1
│   │       ├── attire_2.json
│   │       ├── attire_3.json
│   │       ├── attire_4.json
│   │       └── moves.json           # This wrestler's move slot assignments
│   │
│   ├── ring/                        # Ring and arena assets
│   │   ├── ring.glb
│   │   ├── textures/
│   │   └── arena.glb
│   │
│   ├── animations/                  # Shared animation clips (used by all wrestlers)
│   │   ├── grapple/
│   │   ├── strikes/
│   │   ├── ground/
│   │   ├── turnbuckle/
│   │   └── taunts/
│   │
│   └── audio/
│       ├── sfx/
│       └── music/
│
├── data/                            # Game data and reference documents
│   ├── moves/
│   │   └── Moves.md                 # Master move slot reference document
│   └── characters/
│       └── roster.json              # List of all playable characters
│
├── tools/                           # Dev utilities (not shipped with the game)
│   └── texture-mapper/              # Tool for mapping hashed No Mercy texture filenames to body parts
│
└── README.md
```

---

## Character System

### Models

Character models are built in **Blockbench** using a generic rigged model format and exported as **GLB** files. Babylon.js loads GLB natively, including the skeleton/bone rig and any embedded textures.

The visual style targets N64-era proportions — oversized upper bodies, thick limbs, large heads, hard cube edges. Shading is baked directly into the texture faces rather than relying on dynamic lighting, just like the original hardware did.

Each body part is a separate named mesh in the GLB, corresponding to the parts defined in the texture manifest:

```
head, neck, torso_upper, torso_lower,
shoulder_left, shoulder_right,
upper_arm_left, upper_arm_right,
forearm_left, forearm_right,
hand_left, hand_right,
wristband_left, wristband_right,
thigh_left, thigh_right,
kneepads_left, kneepads_right,
shin_left, shin_right,
boot_left, boot_right,
trunks_front, trunks_back,
trunks_side_left, trunks_side_right
```

### Texture Manifests

Each attire is defined by a JSON manifest that maps body part names to texture filenames. This design allows the game to load textures from ripped No Mercy assets without any modification — you just drop the PNGs into the attire folder and map them in the JSON.

```json
{
  "character": "the_rock",
  "attire": 1,
  "parts": {
    "head": "WWF No Mercy#A010FF85#2#1#2DC66F47_ciByRGBA.png",
    "torso_upper": "WWF No Mercy#234A7097#2#1#1602CE21_ciByRGBA.png",
    "upper_arm_left": "WWF No Mercy#8536CB12#2#1#75C6398E_ciByRGBA.png",
    ...
  }
}
```

`CharacterLoader.js` reads this manifest at load time, finds each named mesh in the GLB, and applies the corresponding texture as a `StandardMaterial` in Babylon.js. Attire switching at the character select screen is handled by swapping which manifest gets loaded — the model itself never changes.

If a body part shares a texture with another part (common in the original game), simply use the same filename in multiple slots.

### Move Assignments

Each wrestler has a `moves.json` file that assigns specific moves from the master move database to their input slots. This is what makes one wrestler feel different from another — they use the same engine and the same animation pool, but their move slot assignments are unique to them.

```json
{
  "character": "the_rock",
  "moves": {
    "front_weak_grapple_1": "Arm Drag",
    "front_weak_grapple_2": "Knee Lift",
    "front_strong_grapple_1": "DDT 01",
    "front_finisher": "Rockbottom",
    "back_finisher": "People's Elbow Setup",
    ...
  }
}
```

The full list of available moves, their power ratings, KO potential, bleed potential, and special features (Pin, Submit, etc.) is documented in `data/moves/Moves.md`.

---

## Move System

### Move Slots

The move system is built around **slots** — named input combinations that each wrestler fills with a move of their choosing. The slot system is faithful to No Mercy's original design.

The top-level slot categories are:

|Category|Description|
|---|---|
|**Grappling**|Front/Back weak and strong grapples, each with 8 directional variants|
|**Standing**|Weak strikes, strong strikes, running attacks, counter attacks|
|**Ground**|Upper/lower body submissions, ground strikes — varies by opponent position (facing up, down, sitting)|
|**Turnbuckle**|Corner grapples, tree-of-woe attacks, flying attacks from top rope|
|**Ringside**|Attacks and grapples through/over the ropes, diving attacks to outside|
|**Apron**|Attacks and grapples from the ring apron|
|**Irish Whip**|Follow-up moves after whipping an opponent into the ropes|
|**Taunt**|Standing, ducking, corner, turnbuckle, celebration, and entryway taunts|
|**Double-Team**|Co-op grapples and flying attacks with a partner|

### Input Logic

Inputs follow the original N64 controller layout, mapped to keyboard/gamepad:

|N64 Button|Action|
|---|---|
|**A (tap)**|Initiate weak grapple|
|**A (hold)**|Initiate strong grapple|
|**B (tap)**|Weak strike|
|**B (hold)**|Strong strike|
|**Control Stick**|Execute finisher (when Special is active)|
|**C-Down**|Run|
|**R**|Counter / reversal|
|**L**|Irish whip / rope interaction|

Directional modifiers (←/→/↑/↓) on the analog stick change which move fires within a grapple state. For example, in a Front Strong Grapple, pressing ↑ + A fires slot `front_strong_grapple_3`, while pressing ↓ + B fires slot `front_strong_grapple_8`.

`InputManager.js` reads raw inputs each frame and passes them to `MoveEngine.js`, which checks the current character state (standing, grappling, downed, running, etc.) and resolves which move slot is being requested. `MoveEngine.js` then looks up that slot in the character's `moves.json` and triggers the corresponding animation and damage/effect logic.

### Move Database

Every move in the game is defined in the master move database (`data/moves/Moves.md`) with the following properties:

|Property|Description|
|---|---|
|**Power**|Damage tier: F (weakest) → E → D → C → B → A → S (strongest), G for special gag moves|
|**KO**|Whether this move can cause a knockout|
|**Bleed**|Whether this move can open a blade job|
|**Feature**|Special outcome: `Pin` (goes straight to pin attempt), `Submit` (applies a submission hold), or `Null`|

Moves are organized by which slot category they are eligible for — for example, finisher-tier moves only appear in the Finisher slot lists.

---

## Game Systems

### Grapple System

`GrappleSystem.js` manages the grapple state machine. When a player initiates a grapple:

1. A proximity and facing check determines if the grapple connects
2. The grapple type (weak/tap A, or strong/hold A) and the opponent's position (front, back, corner, apron) determine which slot category is now active
3. The game enters a **grapple hold state** — both characters are locked together and the attacking player has a short window to input a directional + button combination
4. If no input is made, the grapple is broken
5. If the defending player inputs a reversal (R) with correct timing, the grapple counters

### Finisher System

`FinisherSystem.js` manages the Special meter:

- The meter fills as a wrestler deals and receives damage, and by performing taunts
- When the meter is full, the **Special** state activates — indicated in the HUD
- While Special is active, the Control Stick in a grapple triggers the character's finisher move instead of a regular grapple move
- Performing a finisher empties the Special meter
- Special also upgrades certain ground and turnbuckle moves to their finisher variants

### Match Manager

`MatchManager.js` handles overall match state:

- Tracks health for both wrestlers (displayed as a damage meter, not hit points — heavier moves deal more damage)
- Manages pin attempts — initiating a pin, the count animation, and kickout window
- Manages submission attempts — hold application, struggle mechanic, tap-out detection
- Handles count-out logic for wrestlers outside the ring
- Determines win conditions (pin, submission, KO, count-out, disqualification)
- Triggers the post-match celebration taunt sequence

---

## Adding a New Wrestler

1. **Build the model** in Blockbench following the standard bone/mesh naming convention, export as GLB
2. **Create the character folder** under `assets/characters/your_wrestler/`
3. **Add texture folders** — `base/` for skin textures, plus one folder per attire
4. **Create attire manifests** — one `attire_N.json` per attire, mapping each body part to its texture file
5. **Create `moves.json`** — assign a move from the master database to each input slot
6. **Add to roster** — add an entry to `data/characters/roster.json` pointing to the character folder

No engine code needs to change. The character loader, move engine, and all game systems are entirely data-driven.

---

## Tech Stack

|Tool|Purpose|
|---|---|
|[Babylon.js](https://www.babylonjs.com/)|3D rendering engine, physics, animation|
|[Blockbench](https://www.blockbench.net/)|Character model creation and rigging|
|GLB / glTF|Character model format|
|JSON|Character data, texture manifests, move assignments|
|Vanilla JS|Game logic (no framework)|

---

## 🤝 How to Contribute

We need help from the community to document No Mercy's intricate systems! You can contribute by:

- **Playing and observing** No Mercy to document mechanics
- **Measuring** environmental elements (ring size, distances, etc.)
- **Analyzing** frame data, timing windows, and damage values
- **Writing documentation** in Markdown format
- **Creating JSON schemas** for structured data
- **Organizing** research materials and findings

**No programming experience required for Phase 1!** If you love No Mercy and want to help preserve its design, you can contribute.

---

## 🎮 Design Principles

### Accuracy First

We aim to recreate No Mercy's feel as faithfully as possible. When in doubt, we defer to how No Mercy actually works.

### No Copyrighted Content

The final game will contain:

- ❌ No WWE/WWF characters, logos, or branding
- ❌ No licensed music or copyrighted assets
- ✅ Original characters, arenas, and assets
- ✅ Full customization through JSON and user-provided media

### Open and Moddable

Everything is designed to be:

- Human-readable (JSON, not binary formats)
- Easily customizable (characters, moves, arenas, belts)
- Community-driven (open contribution model)

---

## 📜 License

This project is licensed under the GNU General Public License v3.0. See LICENSE.md for details.

This means:

- ✅ Free to use, modify, and distribute
- ✅ Must remain open-source
- ✅ Derivative works must use the same license

---

## 🙏 Acknowledgments

This project is inspired by the legendary work of:

- **AKI Corporation** — Developers of WWF No Mercy, WWF WrestleMania 2000, WCW/nWo Revenge, WCW World Tour, Virtual Pro Wrestling 64, and Virtual Pro Wrestling 2
- **The No Mercy modding community** — Years of dedication to this game

We are not affiliated with WWE, AKI Corporation, or THQ. This is a fan project created out of love for the gameplay mechanics and design principles of No Mercy.

---

## 📞 Community

- **Issues**: Use GitHub Issues for documentation questions and discussions
- **Discussions**: Share research findings and observations

Let's build something amazing together! 🤼‍♂️
