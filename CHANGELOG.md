## brmau fork (based on v4.02)

Bug fixes on top of v4.02, one pull request each. Details in [`FORK_CHANGES.md`](FORK_CHANGES.md).

- Log: one file per day (UTC) instead of a new file on every map change, which used up the 1024 files per session limit and made saves fail.
- Level/XP math in integers: no more repeated level up message on high levels, exact level boundary, max level 3000 enforced.
- Daily rewards work again (Unix timestamps), can be claimed from the menu and are saved right away. "Next reward" is shown.
- "That's the question" (`why1`) can be unlocked.
- XP HUD on its own channel (`HUD_CHANNEL`, default 6) and only resent when it changes.
- Champion typos and text fixes; comment cleanup.

## v4.02

- Fixed handicap menu inserting new lines when activating handicaps.
- Fixed "Dirty Mag" handicap consuming akimbo uzi ammo -twice- during reload.

## v4.00

- Reworked save system, this new system should be much more efficient with increase server perfomance. Old v3.73 save files are compatible with this update, migration should be done automatically with no issues.
- 3 new achievements have been added.
- Removed ENABLE_FF, ALLOW_PVP_SCORE and Friendly Fire handicap. This was a terrible idea.
- New "Dirty Mag" handicap - Any ammo left unused on the magazine is lost in the next reload.
- New map settings: SIMULATED_LEVEL, NO_SAVE and LIMITED_RESPAWN.
- Server admins can now force handicaps to players via console.
- Server owners can now enable/disable skills, overpower, data saving, or start simulation mode directly, at any time, on any map via console.
- Some obscure bug fixes that I no longer remember.

## v3.XX

- Check the "v3" branch for older changelogs.
