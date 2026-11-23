# Scan Debris Contract Fix

A Stellaris mod for nomadic empires (game **4.5.x and 4.6.0**, tested on both): the **Scan
Debris** contract no longer fails the moment your science ship starts analysing the debris.

Since 4.5.4 the contract cancels itself when no debris is left to scan in its system, and debris
that is being analysed already counts as gone. 4.6.0 did not change that. This mod keeps the
contract alive while its debris waits to be scanned. Debris that really expires still cancels the
contract.

**One edge case:** the mod notices a contract when one of your fleets enters a system, and once
a month. If your science ship is already in the contract's system when you accept, leave the
system and come back before starting the analysis (or wait for the next month).

Additive: no vanilla file is overridden, safe to add to a running campaign and to remove again.
Once Paradox fixes the contract in the game, this mod will be retired.

## Layout

- `scan_debris_fix/` — the mod (only this folder ships).
  - `common/scripted_effects/sdf_effects.txt` — `sdf_refresh_scan_guard`.
  - `common/on_actions/sdf_on_actions.txt`, `events/sdf_events.txt` — monthly, and when a nomad's
    fleet enters a system.

Unofficial fan project, not affiliated with Paradox Interactive. Stellaris and its game data are
Paradox's.
