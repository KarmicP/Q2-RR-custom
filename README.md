# Q2 RR Karmic — a MuffMode server build for Quake II Rerelease

A customized **server-side mod** for the **Quake II Rerelease** (Steam), built on
[MuffMode](https://github.com/DarkMatter-Productions/MuffMode). It's a single
drop-in `game_x64.dll` with **player map voting (votemap)**, population-aware map
rotation, a persistent leaderboard, Steam-ID bans, techs in FFA, several upstream
bug fixes, and more.

> **Just want to play?** You don't need to install anything — join a *Karmic FFA*
> server from the in-game browser. This download is for **hosting** the build yourself.

**Windows x64 · Quake II Rerelease · listen server**

---

## Download & install

Grab the latest from **[Releases](../../releases)**:

- **`KarmicMuffMode-Installer-vX.Y.exe`** — one-click: backs up your vanilla DLL, installs this build.
- **`KarmicMuffMode-vX.Y.zip`** — the same files as scripts you can inspect first.

See **[INSTALL.md](INSTALL.md)** for step-by-step instructions and how to restore vanilla.

**Prefer to compile it yourself?** See **[BUILD-HOWTO.md](BUILD-HOWTO.md)** — clone vanilla
MuffMode v0.60.0, apply `karmic-features-vX.Y.patch` (in this repo / attached to each release),
and build. That patch is also the corresponding GPL source for the binary.

---

## What's added on top of MuffMode

### Gameplay & server operation
- **⭐ Map voting (votemap)** — players choose the next map by vote from a clean,
  **map-only** call-vote menu: the menu shows *only* what's actually votable on this
  server (no clutter of disabled categories). Vote via the in-game menu or
  `callvote map <name>`. The votable pool is server-curated (`g_map_pool`).
- **Population-aware map rotation** — the next map is chosen from a size bucket based
  on how many **humans** are playing (bots don't count): small maps at 1–2 players,
  medium unlocking at 3+, large at 5+. Keeps small games off cathedral-sized maps.
- **Drop bots when humans arrive** — bots fill empty servers, but once *N* humans are
  actively playing (default 2) all bots are removed for a pure-human match; they
  refill if players leave.
- **Anti-camp powerup respawn** — powerup pads respawn on a randomized timer so they
  can't be timed/camped. The delay is re-rolled every cycle (tunable via
  `g_powerup_respawn_min` / `_jitter`).
- **Cross-session Elo leaderboard** — persistent per-player rankings (human-only,
  pairwise Elo) that survive restarts. View with the `leaderboard` console command.
- **Bans by Steam ID** — a persistent ban list keyed to the engine's stable account id,
  with in-game admin `ban` / `unban` / `banlist` and an admin-menu picker. Bots are never banned.
- **Default / boot map** — a configurable fallback map used when the rotation runs dry,
  which also becomes the map the server boots into; the admin's pick survives restarts.
- **Techs enabled in FFA** — the four CTF runes (Disruptor Shield, Power Amplifier,
  Time Accel, AutoDoc) spawn in free-for-all (off by default in stock MuffMode's FFA).
- **Instant-start friendly** — no warmup/countdown by default; players auto-join straight
  into the match.

### Bug fixes (some reported upstream)
- **Powerup activation sound** now plays on the default Quake II Rerelease ruleset
  (was silent — e.g. Quad Damage). *Upstream [PR #232](https://github.com/DarkMatter-Productions/MuffMode/pull/232), merged.*
- **Match-stats menu** now shows real K/D, accuracy and damage instead of all zeros.
  *Upstream [PR #233](https://github.com/DarkMatter-Productions/MuffMode/pull/233), merged.*
- **Auto-join** (`g_dm_auto_join` / `g_dm_force_join`) now works for human players.
  *Upstream [PR #234](https://github.com/DarkMatter-Productions/MuffMode/pull/234).*
- **Techs no longer double-spawn** on instant-start servers (you'd see two of each a few feet apart).
- **Call-vote menu** hides vote categories your server has disabled, instead of listing all of them.

### Reliability
- **Hard map-rotation workaround** — sidesteps an intermittent engine crash on the
  soft "reconnecting" level change at match end by doing a full `map` reload instead.

---

## Configuration

The server is driven by `net.cfg` (included). It sets the hostname, map rotation and
votable pool, bot fill, voting rules, techs/powerups, and the population thresholds.
Edit it to taste — keep it under ~8 KB (the engine drops the tail past that). Start a
listen server with the Steam Launch Option `+exec net.cfg` (auto every launch), or by
typing `exec net.cfg` in the in-game console (one-off, or to reload after editing).

---

## Built on MuffMode — credits & upstream

This is a fork of **[MuffMode](https://github.com/DarkMatter-Productions/MuffMode)** by
DarkMatter Productions, itself built on id Software's Quake II game source (Quake II
Rerelease by Nightdive Studios). All credit for the underlying mod framework goes to the
MuffMode authors. Bug fixes here are contributed back upstream where they're general-purpose.

## License

Licensed under the **GNU General Public License v2.0** — see [LICENSE](LICENSE). This
build is derived from MuffMode (GPL-2.0) and id Software's GPL Quake II source.

**Source availability (GPL §3):** the complete corresponding source for this binary is
available on request — open an issue here or email **karmic.pretizas@gmail.com**, and see
the upstream project for the base framework. *Quake II* and its assets are property of id
Software / ZeniMax; this project ships **no** game assets, only the game logic DLL.
