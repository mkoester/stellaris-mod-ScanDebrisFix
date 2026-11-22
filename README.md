# Scan Debris Contract Fix

A Stellaris mod (game **4.5.x**) for nomadic empires: the **Scan Debris** contract no longer fails
the moment your science ship starts analysing the debris.

In 4.5.4 the contract cancels itself when no debris is left to scan in its system, and debris
that is being analysed already counts as gone. This mod keeps the contract alive while its debris
waits to be scanned. Debris that really expires still cancels the contract.

Additive: no vanilla file is overridden, safe to add to a running campaign.

## Layout

- `scan_debris_fix/` — the mod (only this folder ships).
  - `common/scripted_effects/sdf_effects.txt` — `sdf_refresh_scan_guard`.
  - `common/on_actions/sdf_on_actions.txt`, `events/sdf_events.txt` — monthly, and when a nomad's
    fleet enters a system.

Unofficial fan project, not affiliated with Paradox Interactive. Stellaris and its game data are
Paradox's.
