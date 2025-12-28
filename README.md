# NVMP Remover (Fallout: New Vegas Multiplayer Uninstaller)

A Windows-focused Python tool that removes **NVMP (Fallout New Vegas Multiplayer)** artifacts from a Fallout: New Vegas install **without reinstalling the base game** and without intentionally touching unrelated mods.

This tool is designed to remove NVMP components such as:
- `nvmp_launcher.exe`, `nvmp_start.exe`, `nvmp_storyserver.exe`
- NVMP package/log files like `nvmp.*`
- NVMP-named files/folders inside `Data\...` and `Data\nvse\plugins\...`
- NVMP references in `plugins.txt` / `loadorder.txt`
- NVMP lines in common INI/config files (line-based cleanup only)

✅ **Default behavior is reversible:** removed files are **moved to a timestamped backup folder** rather than deleted.

---

## Why this exists

NVMP often installs **directly into your game directory** (and sometimes mod-manager staging folders). If you just want to play single-player again without losing other mods, reinstalling everything is annoying.

This script does a conservative cleanup by targeting items that **strongly match NVMP signatures** (`nvmp`, `newvegasmp`, `new vegas multiplayer`), and cleaning load lists.

---

## Features

- **Auto-detects** Fallout New Vegas install directory (Steam/GOG common paths + registry + Steam libraries)
- **Scans and removes NVMP artifacts** from:
  - Game folder
  - `Data\`
  - `Data\nvse\plugins\`
  - Common user config folders (Documents / AppData)
  - Common Vortex folders
  - MO2 folders if provided (or discovered nearby the game folder)
- **Cleans NVMP lines** from:
  - `plugins.txt`
  - `loadorder.txt`
  - Common `.ini` files (only lines containing NVMP markers)
- **Backups by default** (quarantine-style)
- Optional **permanent delete** mode (`--delete`)

---

## Requirements

- Windows 10/11 recommended
- Python **3.9+** (3.8 may work, but 3.9+ is recommended)
- Fallout: New Vegas (Steam or GOG)

---

## Repository layout (suggested)

```text
nvmp-remover/
├─ remove_nvmp.py
└─ README.md
```

---

## Installation

Clone or download this repository:

```bat
git clone https://github.com/<yourname>/nvmp-remover.git
cd nvmp-remover
```

No pip dependencies required.

---

## Quick start

### 1) Close NVMP / FalloutNV / Steam
If `nvmp_launcher.exe` or related processes are running, Windows can block removal.

### 2) Run it
```bat
python remove_nvmp.py
```

### 3) If auto-detect fails, specify your game folder
Example Steam path:

```bat
python remove_nvmp.py --game "C:\Program Files (x86)\Steam\steamapps\common\Fallout New Vegas"
```

---

## What happens when you run it?

By default, the script will:

1. Detect your game directory (or use `--game`)
2. Scan common NVMP locations
3. Move NVMP-matching files/folders into a backup folder like:

```text
NVMP_Removed_20251228_153012\
```

4. Remove NVMP references from `plugins.txt` / `loadorder.txt`
5. Remove NVMP lines from common INI/config files (only lines containing NVMP markers)

---

## Modes

### Backup + remove (default, recommended)
Moves matched items into a timestamped backup folder.

```bat
python remove_nvmp.py
```

### Specify backup folder location
By default backup is created in the current working directory. You can change that:

```bat
python remove_nvmp.py --backup-dir "D:\Backups\FNV"
```

### Permanent delete (irreversible)
Deletes matched items instead of backing them up.

⚠️ Only use this if you’re sure and/or have your own backups.

```bat
python remove_nvmp.py --delete
```

---

## Mod manager support

### Mod Organizer 2 (MO2)
If NVMP was installed into MO2’s `mods/overwrite/profiles`, pass your MO2 base folder:

```bat
python remove_nvmp.py --mo2 "D:\Modding\MO2\Fallout New Vegas"
```

### Vortex
Vortex layouts vary based on where you chose staging. If you know your Vortex mods/staging folder, pass it:

```bat
python remove_nvmp.py --vortex "D:\Vortex Mods\falloutnv"
```

If you don’t pass `--vortex`, the script still checks common `%APPDATA%\Vortex\...` locations.

---

## Restore (undo) / Recovery

If you used the default mode, your removed files are stored in the backup folder printed at the end.

To restore:
1. Open the backup folder (e.g. `NVMP_Removed_20251228_153012`)
2. The backup preserves your original path structure
3. Copy/move items back to their original locations (reverse of what the script did)

The script also saves `.bak` files for edited load lists:
- `plugins.txt.bak`
- `loadorder.txt.bak`
- etc.

You can restore those backups if you want NVMP entries back (usually you don’t).

---

## Troubleshooting

### “Permission denied” / Access errors
If your game is in `C:\Program Files (x86)\...`, Windows may block file operations.

✅ Fix: Run PowerShell or Command Prompt as **Administrator**:
- Start Menu → type “PowerShell”
- Right-click → **Run as administrator**
- Run the script again

### Files won’t remove because they’re in use
Close:
- `nvmp_launcher.exe`
- `FalloutNV.exe`
- Steam (optional but helps)
Then run again.

### It didn’t remove everything NVMP ever touched
This tool is conservative on purpose. It removes items that clearly match NVMP signatures.

If NVMP files were renamed to something that doesn’t contain NVMP markers, the script won’t guess—because that risks deleting unrelated mods.

---

## Safety model (what it will and won’t do)

✅ Will remove:
- Files/folders whose *names* match strong NVMP patterns (`nvmp`, `newvegasmp`, etc.)
- NVMP lines inside load lists and INI/config files (line-based cleanup only)

❌ Will NOT:
- Delete your entire `Data` folder
- Wipe your mod manager setup
- Remove unrelated mods that don’t match NVMP signatures

---

## FAQ

### Does this remove NVMP launchers in the game folder?
Yes. Anything like:
- `nvmp_launcher.exe`
- `nvmp_start.exe`
- `nvmp_storyserver.exe`
will be removed (backed up by default).

### Do I need to reinstall my mods after running this?
In most cases, no. The script is designed specifically to avoid touching unrelated mods.

### Can I run it multiple times?
Yes. If NVMP is already removed, it will do little or nothing.

---

## License (suggested)

MIT License (recommended). If you want, add a `LICENSE` file with the MIT text.  
(Keeping the license section here is fine, but GitHub best practice is a separate `LICENSE` file.)

---

## Disclaimer

This project is not affiliated with Bethesda, Obsidian, Steam, or any NVMP team.  
You use this tool at your own risk. Always keep backups of modded game installs.

If you hit an edge case, open an issue and include:
- your install method (manual / MO2 / Vortex)
- where NVMP was installed
- the console output of the script
