# Install — Q2 RR Karmic

A server-side mod for **Quake II Rerelease** (Steam): a single drop-in
`game_x64.dll`. The installer backs up your existing (vanilla) DLL first, then
installs this build. **Windows x64 only.**

> Players joining a Karmic server don't need this — connect from the in-game server
> browser. You only need this to **host** the build yourself.

Download from **[Releases](../../releases)**.

---

## Easiest: the installer .exe

1. Run **`KarmicMuffMode-Installer-vX.Y.exe`**.
2. It auto-finds your Steam Quake II Rerelease, backs up your current
   `game_x64.dll` to `game_x64.dll.vanilla.bak`, and installs the Karmic DLL.
   (If your game is under `Program Files`, accept the admin prompt.)
3. Launch a **listen server**: Steam → Quake II → Properties → **Launch Options**:
   ```
   +exec net.cfg
   ```
   With that set, every launch loads the full config and starts the server automatically.
   You can also start it **on demand from the in-game console** at any time by typing:
   ```
   exec net.cfg
   ```
   (Useful for a one-off, or to reload the config after editing it.)

## Or: the scripts (from the ZIP)

- **Install:** double-click **`Install.cmd`** (add `-WithConfig` to also install `net.cfg`).
- **Restore vanilla:** double-click **`Uninstall.cmd`**.

Point it at a specific install manually:
```powershell
powershell -ExecutionPolicy Bypass -File install-karmic-muffmode.ps1 -GameDir "D:\SteamLibrary\steamapps\common\Quake 2\rerelease" -WithConfig
```

## Or: fully manual

1. Back up `…\Quake 2\rerelease\baseq2\game_x64.dll` (copy to `game_x64.dll.vanilla.bak`).
2. Copy this build's `game_x64.dll` into that `baseq2\` folder.
3. (Optional) copy `net.cfg` into `baseq2\` and edit `hostname` / map list.
4. Launch with `+exec net.cfg`.

---

## Restore vanilla

Run the uninstaller (`Uninstall.cmd`), **or** Steam → Quake II → Properties →
Installed Files → **Verify integrity of game files**.

## Notes & caveats

- **Backup is created once** — re-running the installer never overwrites an existing
  `game_x64.dll.vanilla.bak`, so your true vanilla DLL stays safe.
- **Version match** — the DLL is built against a specific Quake II Rerelease build. If a
  game update changes the DLL API it may need a rebuild; restore vanilla and grab a newer release.
- **No dedicated server on the retail build** — run a **listen** server via the Launch Option above.
- **`net.cfg`** is the server config (hostname, map rotation, bots, techs, etc.). Edit to
  taste; keep it under ~8 KB (the engine silently drops the tail past that).
