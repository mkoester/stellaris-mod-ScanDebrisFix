# Scan Debris Contract Fix

Stellaris mod (game **4.5.x**): a nomad's "Scan Debris" contract no longer fails the day its
science ship starts the analysis. Workspace context: `../AGENTS.md`. Private until published:
publish only if the bug survives 4.6.0 (decided 2026-11-22), together with a Paradox forum bug
report.

Conventions, as in the sibling mods:
- Only `scan_debris_fix/` ships; the top level is dev material. Register it with
  `scripts/link-mod.sh stellaris-mod-ScanDebrisFix/scan_debris_fix` from the workspace root,
  launcher closed.
- Prefix every key, id and flag with `sdf_`. Additive, no vanilla overrides. The one vanilla name
  it writes is the flag `scan_debris_contract_scanned`, on purpose (below).

## The bug (vanilla 4.5.4, files dated 2026-11-13)

`contract_scan_debris` (`common/missions/missions/02_contracts.txt`) got a new `abort_trigger`
clause in 4.5.4: cancel when `NOT has_country_flag = scan_debris_contract_scanned` and the
operator's `any_available_debris` finds none in the target system. Per `triggers.log` that
iterator only sees debris "belong[ing] to available special projects"; a project being analysed is
not available. The flag is set only on completion (`nomads.3035`, from `on_debris_researched` /
`_scavenged` / `_scavenged_and_researched`). So the contract fails the day the analysis starts.
Seen in game 2026-11-22: "it instantly fails". No mod involved: none of the playset's mods touches
the contract, its chain, `nomads.3035` or the debris on_actions.

## The fix

Keep the guard flag set while the debris still waits (`sdf_refresh_scan_guard`, 360-day timed
flag): monthly (`on_monthly_pulse_country` → `sdf.1`) and whenever a fleet of the nomad enters a
system (`on_entering_system_fleet` → `sdf.2`), which covers a ship sent right after accepting.
Only `debris_contract_system@<issuer>` identifies the target system (the save had no flag with
the operator's ID), so the effect goes contract → `issuer` → that star flag.

Why not override `contract_scan_debris` by key: unknown whether missions take the last
definition, and an override would freeze the 4.5.4 contract into 4.6.

Known gap: a ship already in the target system when the contract is accepted, ordered to analyse
before the next monthly pulse, still fails. Expired debris still cancels, up to 360 days late.

## Test run 1: not run yet

To check: the contract survives starting the analysis and completes; `error.log` has no `sdf_`
lines; `has_country_flag = scan_debris_contract_scanned` is set while the contract waits.
