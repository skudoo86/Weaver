# Weaver

**Hunter shot rotation addon for Turtle WoW (1.12.1 + Nampower)**

Weaver automates your shot priority so you can focus on positioning and cooldowns. It weaves Steady Shot with your auto attack timer, fires combos when the window fits, and handles Lock and Load procs — all from a single macro button.

Weaver also acts as a swing timer bridge, exposing `st_timerRange` and `st_timerRangeMax` globals so SuperCleveRoidMacros `[norangedclip]` conditionals work without SP_SwingTimer.

---

## Requirements

ADDONS:
- [Quiver](https://github.com/) — provides the swing timer data Weaver reads from
- [SuperCleveRoidMacros] (https://github.com/jrc13245/SuperCleveRoidMacros) — for `[norangedclip]` macro conditionals
  
MODS:
- [Nampower](https://gitea.com/avitasia/nampower.git) — required for spell queuing and the extended `UnitMana` API 
- [UnitXP_SP3] (https://codeberg.org/konaka/UnitXP_SP3/releases) - required for functionality of some features
  
> **Note:** Remove or disable SP_SwingTimer if you have it installed. It conflicts with Weaver's SCRM bridge.

---

## Installation

1. Download or clone this repository
2. Copy the `Weaver` folder into `World of Warcraft/Interface/AddOns/`
3. Reload or log in — you should see `[Weaver] Loaded. Version 1.01.`

---

## Setup

Create a macro with a single line and bind it to a key:

```
/weaver cast
```

Press it repeatedly during combat to execute your rotation.

---

## Specs

Weaver supports two active specs. Set your spec once and the rotation adapts automatically.

| Command | Spec | Rotation |
|---|---|---|
| `/weaver spec mm` | Marksman | Full MM rotation — Aimed Shot on L&L, Arcane during Enchanted Ammo debuff, Steady filler, Steady+Arcane/Multi combos when enabled.
| `/weaver spec bm` | Beast Master | Kill Command on every press, Steady filler, Steady+Arcane/Multi combos when enabled.
| `/weaver spec sv` | Survival | Disabled — not supported in this version |

---

## Shot Priority

### Marksman (MM)

1. **Aimed Shot** — when Lock and Load is active (with configurable clipping allowance)
2. **Steady + Arcane combo** — when Enchanted Ammo is active, mana above threshold, and within allowable clip.
3. **Arcane Shot** — when Enchanted Ammo is active.
4. **Steady + Multi combo** — when enabled, mana above threshold, and within allowable clip.
5. **Steady Shot** — filler with zero clipping allowed.
6. **During Movement** — Arcane Shot, or Concussive Shot if Arcane is on cooldown.

### Beast Master (BM)

1. **Kill Command** — attempted on every press (off-GCD, fires when available after a crit)
2. **Steady + Arcane combo** — mana above threshold, and within allowable clip.
3. **Steady + Multi combo** — mana above threshold, and within allowable clip.
4. **Steady Shot** — filler with zero clipping allowed.
6. **During Movement** — Arcane Shot, or Concussive Shot if Arcane is on cooldown

---

## Commands

```
/weaver                         Show current status
/weaver help                    Show this command list
/weaver cast                    Fire the rotation (use as a hotkey'd macro)
/weaver spec <mm|bm|sv>         Set your talented spec
/weaver aimed toggle            Toggle Aimed Shot clipping on/off
/weaver aimed time 0.35         Set max Aimed clip window (0.10–0.45s, rounds to nearest 0.05)
/weaver combos <off|as|ms|both> Set combo mode (off / Steady+Arcane / Steady+Multi / Both)
/weaver mana <0-70>             Set mana % threshold for combos (rounds to nearest 10)
/weaver chat <off|combo|clip|all> Control chat notifications
```

---

## Combo Modes

| Mode | Description |
|---|---|
| `off` | No combos — Steady filler only |
| `as` | Steady + Arcane Shot combos only |
| `ms` | Steady + Multi-Shot combos only |
| `both` | Both combo types (default) |

### Clip Allowances

Weaver uses these hardcoded clipping timing based on extensive testing & calculations to balance between auto shot loss vs damage gained.

| Shot | Allowance |
|---|---|
| Aimed Shot (L&L) | Configurable, default 0.35s |
| Steady + Arcane combo | Locked @ 0.35s |
| Steady + Multi combo | Locked @ 0.25s |
| Steady filler | Locked @ 0.00s |

---

## Mana Threshold

The mana threshold controls when Weaver stops attempting combos. Set it based on how long your fights last and what buffs you have.

```
/weaver mana 40    -- stop combos below 40% mana (default)
/weaver mana 0     -- always attempt combos
/weaver mana 70    -- only attempt combos above 70% mana
```

---

## Chat Notifications

| Mode | What gets reported |
|---|---|
| `off` | Nothing (default) |
| `combo` | Confirmed combo shots (e.g. `COMBO! (Steady>Arcane)`) |
| `clip` | Auto shot clips with time lost |
| `all` | Both combos and clips |

---

## Saved Settings

All settings persist between sessions via `Weaver_Store`:

| Setting | Default | Description |
|---|---|---|
| `Spec` | `mm` | Active spec |
| `AimedGraceEnabled` | `true` | Aimed Shot clip allowance on/off |
| `AimedGraceSeconds` | `0.35` | Max Aimed clip window in seconds |
| `ComboMode` | `both` | Active combo mode |
| `ManaThreshold` | `40` | Mana % floor for combos |
| `ChatMode` | `off` | Chat notification level |

---

## SCRM Bridge

Weaver automatically populates the globals `st_timerRange` and `st_timerRangeMax` each frame, making `[norangedclip]` work in SuperCleveRoidMacros without SP_SwingTimer. The bridge activates automatically if SP_SwingTimer is not loaded.

---

## Notes

- Rapid Fire is detected automatically — cast times adjust to 0.6s while active
- For BM, Kill Command is attempted every rotation press. It silently fails when not available
- The Nampower extended `UnitMana` API is used to read caster mana while shapeshifted (relevant for future druid support)
- Combo detection uses the combat log to confirm both shots landed within a 1.5s window

---

## Version History

| Version | Notes |
|---|---|
| 1.01 | Added spec system (MM/BM/SV), combo mode selector, mana threshold, debug mode |
| 1.00 | Initial release — MM rotation, SCRM bridge, Quiver integration |

---

*Made by Redchurch for Turtle WoW hunters.*
