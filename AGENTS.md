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

## Still in 4.6.0 (checked 2026-11-23)

4.6.0 "Corona Borealis" left `common/missions/missions/02_contracts.txt` untouched (file dated
2026-11-13, the 4.5.4 one): the `abort_trigger` clause is the same, and `nomads.3035` still sets
the flag only on completion. No line of the 4.6.0 release notes names the contract. So the bug
survives 4.6.0, the condition for publishing this mod with a forum bug report. Test run 1 on 4.6
comes first.

## Test run 1: not run yet

How (2026-11-23): a throwaway game as a nomad empire, `event devtools.5` (communications), select a
star near the arkship, `event sdf.90` (`events/sdf_debug_events.txt`: an AI empire loses a
corvette there, its debris; a day later `sdf.91` issues the contract there, untargeted). Accept
it, send a science ship, start the analysis at once. Script-made debris does count as
`any_available_debris` for the issuer (run 2's log); `game.log` shows each step's `SDF test` line.

Tries so far (2026-11-23, throwaway game `roamingcybergang2`): three helper contracts, all
aborted **on their issue day** (`MESSAGE_CONTRACT_ABORTED` dated the issue day), so the fix was
never reached. The issuer saw the debris (`sdf.91`'s log), but `issue_contract` had
`target = <us>`: a targeted contract's scope is the target (the save's mission: `scope` country 0,
`from` the issuer), and `issued_abort_trigger` opens with `is_nomadic = yes` — true for the nomad
it targets. Vanilla never targets `contract_scan_debris`; `sdf.91` no longer does. Any nomad may
now take it: accept before an AI nomad does.
Run 3 (untargeted, 2200.11.02): issued (the issuer's `scan_debris_contract_active` is in the
12.01 save), then gone without a message, and the debris with it: an untargeted contract that
aborts before acceptance tells no one. What removes the debris within days is open; `sdf.92`
logs daily for five days whether the issuer still sees it.

To check: the contract survives starting the analysis and completes; `error.log` has no `sdf_`
lines; `has_country_flag = scan_debris_contract_scanned` is set while the contract waits.
