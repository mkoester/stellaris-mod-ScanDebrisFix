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
      case is in the store text (`store/`).
- [x] `version="1.0.0"`, `supported_version="v4.6.*"`, tags, `picture="thumbnail.png"`
      (512×512, our own art).
- [x] `ls -A scan_debris_fix` lists only `common/ descriptor.mod events/ thumbnail.png` (the
      launcher uploads everything, including a stray `.claude/`).
- [x] No Discord forum: feedback goes to the Workshop page's comments (user, 2026-11-23).
- [ ] Launcher closed → `scripts/link-mod.sh stellaris-mod-ScanDebrisFix/scan_debris_fix` →
      launcher open. On Linux the upload dialog may not see a **symlinked** mod: then copy the
      folder into `<user dir>/mod/` and upload the copy.
- [x] Upload to **Steam Workshop** (description: `store/scan_debris_fix.bbcode`, then the "More
      from me" block). A first upload starts **Hidden**: set it Public. The launcher writes
      `remote_file_id` into `descriptor.mod`: commit it.
- [x] Upload to **Paradox Mods** (the 1.0.0 text was pasted and formatted in the website editor;
      `scripts/store_texts.py` fills it since). Its ID is `remotePdxId` in the launcher's
      `launcher-v2.sqlite` (`mods` table; read a copy).
- [x] IDs into the table below; this mod's line into `../docs/store-more-from-me.md` (block
      and "Where it goes" table); a line in `~/Projects/workspace_oss/releases/mods.txt`. The
      other mods' pages show it after their next upload (`scripts/store_texts.py --all` first).
- [x] Post `docs/forum-bug-report.md` in Paradox's Stellaris bug report forum (2026-11-23, linked
      above).
- [x] GitHub repo public (2026-11-23); workspace `AGENTS.md` and `bootstrap.sh` list it.

## Published IDs

| Store | Item ID | URL |
|---|---|---|
| Steam Workshop | `3844863561` | https://steamcommunity.com/sharedfiles/filedetails/?id=3844863561 |
| Paradox Mods | `168883` | https://mods.paradoxplaza.com/mods/168883/Any |

Published 2026-11-23 (Workshop public; Paradox Mods still answered 403 "User does not have access" to anonymous readers that evening, like Ancient Highway's 168844).

Paradox bug report: https://forum.paradoxplaza.com/forum/threads/scan-debris-contract-fails-the-day-the-science-ship-starts-analysing-the-debris.1951553/ (posted 2026-11-23)

## Store description

`store/scan_debris_fix.bbcode` is the Steam Workshop text (BBCode). `scripts/store_texts.py` in the workspace (run by `slink`, or on its own with the launcher closed) writes it into the launcher's upload form, with the "More from me" block appended, and makes the Paradox Mods HTML and short description from it. Edit the file, never the form.
