# Disk cleanup (2026-09-20)

After documenting, disposable Wine lab weight was removed from the Linux NVMe to free space for WinApps.

## Removed (safe lab junk)
- `/tmp/ps-fix` — screenshots / smoke logs  
- `~/WineApps/build` — wine-dcomp build tree  
- `~/WineApps/quarantine` — quarantined dumps  
- Unused runtimes: `wine-custom`, `wine-custom-vanilla-*`, old tarballs (`wine-11.10-*`, `wine-9.6*`, duplicate adobe tarballs if present)  
- Tiny probe prefixes: `probe-proton`, `test-custom`

## Kept (for now)
- `~/WineApps/docs` + this repo  
- `~/WineApps/runtimes/wine-dcomp` (reference runtime)  
- `~/.config/wineapps/*.conf`  
- Prefixes still used or large app installs: `solidworks`, `photoshop`, `coreldraw`, `ps2024`, `swlicense` — **not** deleted automatically (revisit after WinApps is up)  
- External `WD SSD` WineApps media — untouched  

Reclaim estimate from removed trees: on the order of **~10+ GiB** on `/` (build+quarantine+tmp+extra runtimes), exact number depends on what was present.
