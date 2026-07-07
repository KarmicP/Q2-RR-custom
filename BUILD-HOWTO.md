# Build from source — recompile MuffMode + Karmic features (Windows x64)

Prefer not to run the prebuilt binary? You can rebuild the exact `game_x64.dll`
yourself by cloning **vanilla MuffMode v0.60.0** and applying our patch, then
building. This is also the corresponding source under the GPL.

The patch (**`karmic-features-vX.Y.patch`**, in this repo and attached to each
[Release](../../releases)) contains only our source changes: it applies cleanly
onto MuffMode `v0.60.0`.

---

## 1. Prerequisites (one-time)

- **Visual Studio 2022 Build Tools** with the *Desktop development with C++* workload
  — MSVC v143 (v14.4x) and a Windows 10/11 SDK (e.g. 10.0.26100).
- **Git**.
- **vcpkg** with `fmt` and `jsoncpp` for the static triplet:
  ```powershell
  git clone https://github.com/microsoft/vcpkg
  .\vcpkg\bootstrap-vcpkg.bat
  .\vcpkg\vcpkg install fmt:x64-windows-static jsoncpp:x64-windows-static
  ```

## 2. Get vanilla MuffMode v0.60.0 and apply the patch

```powershell
git clone https://github.com/DarkMatter-Productions/MuffMode
cd MuffMode
git checkout v0.60.0

# copy karmic-features-vX.Y.patch into this folder first, then:
git apply --check karmic-features-vX.Y.patch   # verify it applies cleanly
git apply karmic-features-vX.Y.patch           # apply it
```

> No `git`? You can also `patch -p1 < karmic-features-vX.Y.patch` from the repo root.

## 3. Build (from a VS 2022 developer shell)

The build script does **not** locate MSBuild itself, so run it inside a
*Developer PowerShell for VS 2022* (or after importing the VS dev environment).
Point `VCPKG_ROOT` at your vcpkg checkout, then:

```powershell
$env:VCPKG_ROOT = "C:\path\to\vcpkg"
.\scripts\ci\build-msbuild.ps1 -Configuration Release -Platform x64
```

Optional — run the fast host tests (pure logic: bans, ranks, map-size, bot-fill, …):

```powershell
.\scripts\ci\run-host-tests.ps1 -Configuration Release -Platform x64
```

## 4. Result & install

The DLL lands at:

```
build\msbuild\x64\Release\game_x64.dll
```

Copy it into your Quake II Rerelease `…\Quake 2\rerelease\baseq2\` (back up the
existing `game_x64.dll` first), or use the installer from the ZIP release. Launch a
listen server with the Steam Launch Option `+exec net.cfg` (or `exec net.cfg` in the
in-game console).

---

## Features in this build — advantage & usage

Everything the patch adds on top of vanilla MuffMode, why it helps, and how to drive
it. Cvars go in `net.cfg`; admin actions are in the in-game **admin menu** or console.

### Gameplay

| Feature | Advantage | Usage |
|---|---|---|
| **⭐ Map voting (votemap)** | Players pick the next map; the call-vote menu is **map-only** and shows just what's votable here — no clutter, no disabled categories | `g_allow_voting 1`, `g_vote_flags` bitmask (map-only = `131070`), votable set = `g_map_pool`. Players use the in-game vote menu or `callvote map <name>` |
| **Population-aware map rotation** | Small games stay on small maps; big maps unlock only when there are enough players — no 2-player matches on a cathedral | `g_map_size_logic 1`; fill `g_map_pool_small` / `_medium` / `_large`; thresholds `g_map_medium_min 3`, `g_map_large_min 5` (counts humans, not bots) |
| **Drop bots when humans arrive** | Bots keep an empty server lively, then get out of the way for a pure-human match | `g_bot_human_cutoff 2` (0 = off). At ≥N actively-playing humans all bots are kicked; they refill if players leave |
| **Anti-camp powerup respawn** | Pads can't be "timed" and camped — respawn is a floor plus fresh random jitter every cycle | `g_powerup_respawn_min` (floor, seconds) + `g_powerup_respawn_jitter` (random seconds added on top); tune to taste. Applies to quad/invuln/etc. incl. team pads |
| **Techs in FFA** | Adds the 4 CTF runes (Disruptor Shield, Power Amp, Time Accel, AutoDoc) to free-for-all for variety | `g_allow_techs 1` (`"auto"` is CTF/Horde-only, so FFA needs the explicit `1`) |
| **Faster weapon flow** | Snappier fights; weapons come back quickly | `g_weapon_respawn_time 10` (Q2 default 30) |
| **Instant start & auto-join** | Host and joiners spawn straight into the match — no warmup, no join menu | `g_dm_do_warmup 0`, `g_warmup_countdown 0`, `g_dm_auto_join 1` |

### Server operation

| Feature | Advantage | Usage |
|---|---|---|
| **Bans by Steam ID** | Bans survive name/IP changes and reconnects (keyed to the stable account id); bots are never banned | Admin: `ban <slot> [reason]`, `unban <social_id>`, `banlist`, or the admin-menu picker. Persisted to `bans.cfg` |
| **Cross-session Elo leaderboard** | Persistent competitive rankings that survive restarts, driving return play | Auto-recorded at match end (≥2 humans, human-only Elo). View with the `leaderboard` console command |
| **Default / boot map** | Deterministic boot map + a safe fallback if the rotation runs dry | `g_default_map <token>`, admin "Set Default Map", or `defaultmap <token>`; persisted, survives restart |
| **Hard map-rotation workaround** | Avoids an intermittent engine crash on the soft level change at match end | `g_hard_map_rotation 1` (trade-off: full server reload per rotation) |

### Fixes (transparent — no cvars)

- **Powerup activation sound** plays on the default Q2RE ruleset (Quad etc. were silent). *(upstream PR #232, merged)*
- **Match-stats menu** shows real K/D, accuracy, damage instead of zeros. *(upstream PR #233, merged)*
- **Human auto-join** actually works (`g_dm_auto_join`/`g_dm_force_join` were dead for humans).
- **Techs no longer double-spawn** on instant-start servers.
- **Call-vote menu** hides vote categories the server has disabled.

> Full config reference lives in `net.cfg` / `net-notes.txt` in the source tree.

## Notes

- The patch targets **MuffMode v0.60.0** specifically. Against a newer upstream it may
  conflict; check out `v0.60.0` for a clean apply.
- Two of our fixes are already merged upstream (powerup activation sound, match-stats
  menu) — so on newer upstream they'd be redundant. The v0.60.0 base keeps everything
  consistent.
- Building produces a **Windows x64** DLL. Other platforms need their own toolchain.
- Licensed **GPL-2.0** — see `LICENSE`.
