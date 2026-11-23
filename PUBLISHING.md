# Publishing

Store copy and checklist for **Scan Debris Contract Fix**. Both stores are uploaded through the
**Paradox Launcher** (Mod Tools → Upload Mod). Only `scan_debris_fix/` ships. `assets/thumbnail.svg`
is the thumbnail source (our own art), rendered with
`rsvg-convert -w 512 -h 512 assets/thumbnail.svg -o scan_debris_fix/thumbnail.png`.

The mod exists only until Paradox fixes the contract (user, 2026-11-23): the forum bug report is in
`docs/forum-bug-report.md`. When a game update fixes it, say so in both store descriptions and
retire the mod (`AGENTS.md`).

## Checklist (first release, 1.0.0)

- [x] The bug survives 4.6.0, and the fix works there (`AGENTS.md`, "Test run 1"); its one edge
      case is in the store texts below.
- [x] `version="1.0.0"`, `supported_version="v4.6.*"`, tags, `picture="thumbnail.png"`
      (512×512, our own art).
- [x] `ls -A scan_debris_fix` lists only `common/ descriptor.mod events/ thumbnail.png` (the
      launcher uploads everything, including a stray `.claude/`).
- [x] No Discord forum: feedback goes to the Workshop page's comments (user, 2026-11-23).
- [ ] Launcher closed → `scripts/link-mod.sh stellaris-mod-ScanDebrisFix/scan_debris_fix` →
      launcher open. On Linux the upload dialog may not see a **symlinked** mod: then copy the
      folder into `<user dir>/mod/` and upload the copy.
- [ ] Upload to **Steam Workshop** (description: BBCode below, then the "More from me" block from
      `../docs/store-more-from-me.md`). A first upload starts **Hidden**: set it Public. The
      launcher writes `remote_file_id` into `descriptor.mod`: commit it.
- [ ] Upload to **Paradox Mods** (plain text below; format headings, lists and links with the
      website editor, then check the saved page). Its ID is `remotePdxId` in the launcher's
      `launcher-v2.sqlite` (`mods` table; read a copy).
- [ ] IDs into the table below; this mod's line into both blocks of
      `../docs/store-more-from-me.md` (and its "Where it goes" table), then re-paste the blocks
      into every other public mod's descriptions; a line in
      `~/Projects/workspace_oss/releases/mods.txt`.
- [ ] Post `docs/forum-bug-report.md` in Paradox's Stellaris bug report forum; link the thread
      here.
- [ ] GitHub repo public; workspace `AGENTS.md`: move ScanDebrisFix to the public list.

## Published IDs

| Store | Item ID | URL |
|---|---|---|
| Steam Workshop | — | — |
| Paradox Mods | — | — |

Paradox bug report: — (not posted yet)

## "More from me" lines (for `../docs/store-more-from-me.md`, once the IDs exist)

```bbcode
[*][url=https://steamcommunity.com/sharedfiles/filedetails/?id=WORKSHOP_ID]Scan Debris Contract Fix[/url] — nomads: the Scan Debris contract no longer fails the moment your science ship starts the analysis.
```

```text
• Scan Debris Contract Fix — nomads: the Scan Debris contract no longer fails the moment your science ship starts the analysis. https://mods.paradoxplaza.com/mods/PDX_ID/Any
```

---

## Store description — Steam Workshop (BBCode)

```bbcode
[h1]Scan Debris Contract Fix[/h1]

For nomadic empires (Nomads DLC): the [b]Scan Debris[/b] contract no longer fails the moment your science ship starts analysing the debris.

[h2]The bug[/h2]
Since 4.5.4 the contract cancels itself as soon as there is no debris left to scan in its system. Debris that is being analysed already counts as gone, so the contract fails the day you start the analysis, the one thing it asks you to do. 4.6.0 did not change that.

[h2]The fix[/h2]
The mod keeps the contract alive while its debris waits to be scanned. Debris that really expires still cancels the contract, as it should.

[h2]One edge case[/h2]
The mod notices a contract when one of your fleets enters a system, and once a month. If your science ship is already in the contract's system when you accept, [b]leave the system and come back[/b] before starting the analysis, or wait for the next month.

[h2]Compatibility[/h2]
[list]
[*]Built for Stellaris 4.6.x (tested on 4.6.0), also works on 4.5.x (tested on 4.5.4). Needs the Nomads DLC to matter.
[*]Changes no vanilla files: two hidden events and two on_action hooks. Safe to add to a running campaign, and to remove again.
[*]Changes the checksum: no achievements or Ironman while it is active.
[*]Once Paradox fixes the contract in the game, this mod will be retired.
[/list]

[h2]Bugs & feedback[/h2]
Leave a comment below.

[i]Unofficial fan project, not affiliated with Paradox Interactive.[/i]
```

---

## Store description — Paradox Mods (plain text)

```text
Scan Debris Contract Fix

For nomadic empires (Nomads DLC): the Scan Debris contract no longer fails the moment your science ship starts analysing the debris.

The bug
Since 4.5.4 the contract cancels itself as soon as there is no debris left to scan in its system. Debris that is being analysed already counts as gone, so the contract fails the day you start the analysis, the one thing it asks you to do. 4.6.0 did not change that.

The fix
The mod keeps the contract alive while its debris waits to be scanned. Debris that really expires still cancels the contract, as it should.

One edge case
The mod notices a contract when one of your fleets enters a system, and once a month. If your science ship is already in the contract's system when you accept, leave the system and come back before starting the analysis, or wait for the next month.

Compatibility
• Built for Stellaris 4.6.x (tested on 4.6.0), also works on 4.5.x (tested on 4.5.4). Needs the Nomads DLC to matter.
• Changes no vanilla files: two hidden events and two on_action hooks. Safe to add to a running campaign, and to remove again.
• Changes the checksum: no achievements or Ironman while it is active.
• Once Paradox fixes the contract in the game, this mod will be retired.

Bugs & feedback
Leave a comment on the mod's Steam Workshop page: https://steamcommunity.com/sharedfiles/filedetails/?id=WORKSHOP_ID

Unofficial fan project, not affiliated with Paradox Interactive.
```
