# Disk cleanup (2026-09-20)

After documenting, disposable Wine lab weight was removed from the Linux NVMe to free space for WinApps.

## Removed
- `/tmp/ps-fix`
- `~/WineApps/build`
- `~/WineApps/quarantine`
- Runtimes: `wine-custom`, `wine-custom-vanilla-11.16`, old tarballs (`wine-11.10-*`, `wine-9.6*`)
- Prefixes: `probe-proton`, `test-custom`

## Result
- Approx **~14 GiB** freed on `/` (NVMe)
- Free space after cleanup: about **64 GiB** (was ~51 GiB)

## Kept (revisit after WinApps works)
| Path | ~Size | Why kept |
|------|-------|----------|
| `wineprefixes/solidworks` | 12G | until WinApps |
| `wineprefixes/photoshop` | 7G | legacy |
| `wineprefixes/coreldraw` | 5.7G | until WinApps |
| `wineprefixes/ps2024` | 2.1G | PS2024 lab |
| `wineprefixes/swlicense` | 1.7G | SW license |
| `runtimes/wine-dcomp` | ~2G | reference |
| External WD SSD `WineApps` | ~19G | media — untouched |

To free another ~25–30G later: remove unused prefixes once WinApps covers SW/PS/Corel.
