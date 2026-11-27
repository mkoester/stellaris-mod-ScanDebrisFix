# Scan Debris Contract Fix

Stellaris mod (game **4.5.x and 4.6.0**): a nomad's "Scan Debris" contract no longer fails the day its
science ship starts the analysis. Workspace context: `../AGENTS.md`. Public since 1.0.0
(2026-11-23), with a Paradox forum bug report; retired once vanilla fixes the contract.

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

## Still in 4.6.0 (checked 2026-11-23)

4.6.0 "Corona Borealis" left `common/missions/missions/02_contracts.txt` untouched (file dated
2026-11-13, the 4.5.4 one): the `abort_trigger` clause is the same, and `nomads.3035` still sets
the flag only on completion. No line of the 4.6.0 release notes names the contract. So the bug
survives 4.6.0, the condition for publishing this mod with a forum bug report. Test run 1 on 4.6
comes first.

## Still in 4.6.1 (open beta, checked 2026-11-27)

`02_contracts.txt` is still the 4.5.4 file (dated 2026-11-13, with the same `abort_trigger`).
`nomads.3035` changed file but not behaviour: it still sets the flag only on completion (a
10-day timed flag), from the same three debris on_actions. The 4.6.1 notes and the preliminary
4.6.2 notes do not name the contract. So the mod stays. Once 4.6.1 goes live, change "4.6.0"
to "4.6.x" in the README and store text.

## Test run 1: passed (2026-11-23, game 4.6.0)

The user's campaign `roamingcybergang_387306957`, save `2227.09.01` (a contract accepted
2227.06.01, analysis not started, saved before the mod existed). Control without the mod: the
contract failed when the analysis started — **the bug survives 4.6.0**. With the mod: it survived
the analysis. The ship had to leave the system once first: the known gap below (it was in the
system before the guard was set). No `sdf_` line in `error.log`. The README names the gap and
its workaround. Retire the mod once vanilla fixes the contract (user); the bug report:
https://forum.paradoxplaza.com/forum/threads/scan-debris-contract-fails-the-day-the-science-ship-starts-analysing-the-debris.1951553/

## Test run 1: how it was found

How (decided 2026-11-23, after seven helper runs): in the user's real campaign, accept the next
Scan Debris contract the game issues by itself, send a science ship, start the analysis at once.
Check `game.log`/`error.log` and a save during the analysis. The helper events (`sdf.90`–`.94`)
are removed: debris made by script lasts about a day (runs below), so no helper contract
survived until it could be accepted.

Helper runs (2026-11-23, throwaway game `roamingcybergang2`; helpers since removed): three
helper contracts, all aborted **on their issue day** (`MESSAGE_CONTRACT_ABORTED` dated the issue day), so the fix was
never reached. The issuer saw the debris (`sdf.91`'s log), but `issue_contract` had
`target = <us>`: a targeted contract's scope is the target (the save's mission: `scope` country 0,
`from` the issuer), and `issued_abort_trigger` opens with `is_nomadic = yes` — true for the nomad
it targets. Vanilla never targets `contract_scan_debris`; `sdf.91` no longer does. Any nomad may
now take it: accept before an AI nomad does.
Run 3 (untargeted, 2200.11.02): issued (the issuer's `scan_debris_contract_active` is in the
12.01 save), then gone without a message, and the debris with it: an untargeted contract that
aborts before acceptance tells no one. `sdf.92` logs daily for five days whether the issuer
still sees it. Run 4 (2201.01.02): seen on the issue day, gone the next — and gone from the save,
not only unavailable (debris lasts `DEBRIS_DAYS` 1800). Suspect, unconfirmed: our own arkship was
in that system (Brightpool, runs 3 and 4) with the debris policy on Scavenge. Next: a system
without our fleets. Selecting a star where we have no fleet does not work in game (user), so
`sdf.93` (nothing selected) picks a system next to our capital without our fleets; `sdf.90`
(selected star) warns when ours are there. Their shared body: `sdf_test_wreck` and
`sdf_test_can_start` (`common/scripted_*/sdf_test_*.txt`, test-only).
Run 5 (Higma, 2201.04.02, no fleet of ours there): gone the day after again. So not scavenging.
Runs 2-5 wrecked the issuer's own corvette; vanilla destroys another empire's fleet for the
victor. Now a third AI empire's corvette.
Run 6 (Korach, 2201.05.02): the third empire's wreck changed nothing — gone the day after; the
user never saw the contract even on its issue day. Every issuer keeps `scan_debris_contract_active`
(`on_stop` clears it only with an operator), so all stopped unaccepted. Open: does the debris
vanish by itself (and the contract with it), or does the contract's stop take the debris? Control:
`sdf.94`, the same debris with no contract, logged for five days.
**Run 7 (control, Higma, 2201.06.02): no contract, and the debris was still gone the next day.**
Debris made by `destroy_and_spawn_debris_for` lasts about a day whatever happens (vanilla debris:
`DEBRIS_DAYS` 1800). Every helper contract aborted for that reason, before anyone could accept:
the helpers `sdf.90`–`.94` cannot test the fix. A test needs debris from real combat, or a Scan
Debris contract the game issues by itself.

To check: the contract survives starting the analysis and completes; `error.log` has no `sdf_`
lines; `has_country_flag = scan_debris_contract_scanned` is set while the contract waits.
