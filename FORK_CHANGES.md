# Changes in this fork (`brmau` branch)

Everything that differs from the original SCXPM v4 ([JulianR0/CLevels](https://github.com/JulianR0/CLevels), branch `v4`, commit `eb44805`). Each change was made in its own pull request, tested on a local Sven Co-op server and reviewed before merging. Earlier versions of most of them have been running on the [BR] Mau server ([svencoop.mtavaresozaki.com.br](https://svencoop.mtavaresozaki.com.br)).

Only `src/SCXPM.as` and `src/SCXPM_AHandler.as` are changed. **The save format is unchanged**, so existing v4 saves keep working.

## Upgrading from the original v4

Replace the two files in `scripts/plugins/` (or wherever your `default_plugins.txt` points), then restart the server or run `as_reloadplugin` for both plugins and change map. Things that behave differently:

| What | Before | Now |
|---|---|---|
| Log files | one file per map change, with a garbage name | one file per day, `LOG_YYYY-MM-DD.log`, in **UTC**; lines are `HH:MM:SS UTC - message` |
| XP HUD channel | HUD text channel 1 | channel 9 (`HUD_CHANNEL` constant at the top of `SCXPM.as`) |
| XP needed for a level | up to 1 XP different from the level formula | exactly the first XP of the next level (half of the levels need 1 XP less) |
| Daily rewards | never became available | available every 20 h, can also be claimed from the menu |
| `why1` ending | game ends right away | game ends 5 s after the end trigger |

## Pull requests

### #1 Fork setup
README section about the fork and a "modified" line in the header of both files, as GPL-3.0 asks. The original copyright and license text are unchanged.

### #2 Comment cleanup
Removed commented-out code and joke comments. After stripping every comment the code is identical to the original, so behavior can't change.

### #3 HUD: own text channel, only resend when it changes
- **Problem:** the XP HUD was sent every second on HUD channel 1, the default channel of map texts (`game_text`). Map messages showed up for a moment and were then wiped.
- **Fix:** the HUD uses its own channel (`HUD_CHANNEL = 9`) and is only resent when the text or the player's HUD settings change, or once a minute. It is always sent on map start, when a player joins and when a map shows the HUD again.
- Channel 9 was picked after scanning 500+ maps: map texts use channels 1-4 (about 17,500 `game_text`) and a few use 5-8, 10 and 11, but none use 9. If your maps or other plugins use channel 9, change `HUD_CHANNEL`.

### #4 Log: really one file per day
- **Problem:** `DateTime( UnixTimestamp() )` returns wrong date parts on the server, so `SCXPM_Log` created a new file with a garbage name on every map change. The AngelScript file system only allows **1024 files written per server session**. After about a week every write failed, player saves included, until the server restarted.
- **Fix:** the date is computed from the Unix timestamp (`SCXPM_UnixToUTC`), in UTC.

### #5 Level math in integers, exact level boundary, max level 3000
- **Problem:** the level formula used floats and drifted on high XP, so "You reached level X!" repeated every second on high levels. A player stopped exactly at the max XP stayed at level 2999. The per-second XP update also sent the whole XP through a float, losing precision above 16.7M XP.
- **Fix:**
  - `scxpm_calc_lvl`, `scxpm_calc_xp` and `scxpm_calcneedxp` use exact integer math with one shared level boundary;
  - XP from frags is added as a gain;
  - no XP gain or level up past 3000;
  - a level gift can't take the target past 3000, and it now updates the target's XP goal.

### #6 Daily rewards
- **Problem:** daily rewards used `DateTime`/`TimeDifference` (wrong on the server, see #4), so the reward never became available. The menu's "Next reward" line was always empty.
- **Fix:**
  - the next reward time is a plain Unix timestamp;
  - opening the Daily Rewards menu claims a reward that is due;
  - every reward and the new streak are written to disk right away;
  - an XP reward that crosses a level gives the full level up;
  - "Next reward" is shown.

### #7 Why1: "That's the question" can be unlocked
- **Problem:** the map's end trigger also fired its `game_end`, so the game ended before the achievement check could run. Nobody could unlock it.
- **Fix:** the map's `game_end` is renamed and fired by a 5 second `trigger_relay` (no BSP change). If the map has no such `game_end`, a warning is logged and nothing changes.

### #8 Text fixes
Champion typos (wielder, player, career, daunting), a clearer admin message for `.xp_addxp` on a max level player, and a reworded XP Devourer honorific mention.

## Known issues (planned)

- Players who join while Steam authentication is still pending (`STEAM_ID_PENDING`) load an empty profile, and their next save can overwrite their real one.
- Level gifts don't take skill points back from the giver, and gifts and the medals-to-levels menu reset partial progress and can push XP past the max.
- Happy Hour (`GetHour()`) and the Champion screen date still use `DateTime`.
- Achievements that are checked by polling right before a map ends may have the same problem as `why1`.
