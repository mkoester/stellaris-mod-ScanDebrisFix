# Bug report for Paradox's forum

Draft for the Stellaris bug report forum (forum.paradoxplaza.com, "Stellaris Bug Reports"). Fill
the form's fields with the sections below; attach the save. Posted 2026-11-23:
https://forum.paradoxplaza.com/forum/threads/scan-debris-contract-fails-the-day-the-science-ship-starts-analysing-the-debris.1951553/

**Attach:** `roamingcybergang_387306957/2227.09.01.sav` (a 4.5.4 save that loads in 4.6.0: the
contract is accepted, its analysis not started yet). Without mods enabled, from a copy.

---

**Short summary**

Scan Debris contract fails the day the science ship starts analysing the debris

**Game version**

4.6.0 "Corona Borealis" (3d1a); first seen in 4.5.4.

**What OS are you playing on?** Linux (native build)

**Mods:** none for the reproduction.

**Description**

The Nomads contract "Scan Debris" (`contract_scan_debris`) is cancelled ("Contract Abandoned …
Don't fail us again …", no reward) as soon as the operator's science ship starts analysing the
debris it asks to scan.

Cause, in `common/missions/missions/02_contracts.txt` (`contract_scan_debris`, `abort_trigger`,
lines 1822–1844 in 4.6.0): the contract is cancelled when

```
AND = {
	NOT = { has_country_flag = scan_debris_contract_scanned }
	NOT = { any_available_debris = { solar_system = { is_same_value = fromfrom } } }
}
```

`any_available_debris` only iterates "debris belong[ing] to available special projects"
(`triggers.log`). Debris whose analysis has started is no longer an available project, so the
second condition becomes true on the first day of the analysis. The guard flag
`scan_debris_contract_scanned` is only set when the analysis **completes** (`nomads.3035`,
`events/nomads_events_1.txt` line 11587, from `on_debris_researched` / `_scavenged`), so it does
not protect the analysis period, only the day of completion that the comment above the clause
mentions. 4.6.0 did not change this file.

**Steps to reproduce**

1. Play a nomadic empire; accept a "Scan Debris" contract from another empire.
2. Send a science ship to the debris and start the analysis.
3. The contract is abandoned at the next daily check, with no reward.

With the attached save: send the science ship to the contract's debris and start the analysis.

**Expected:** the contract stays active while its debris is being analysed and completes when the
analysis does.

**Suggested fix:** also accept debris that is being analysed in that system, or set the guard
when the analysis starts (not only when it ends), or check `has_star_flag =
debris_contract_system@<issuer>` on the target system (set in `on_accept`) instead of the
available-debris iterator once the contract is accepted.

A workaround mod keeps the guard flag set while the contract's debris waits: Scan Debris Contract
Fix (Steam Workshop: https://steamcommunity.com/sharedfiles/filedetails/?id=3844863561).
