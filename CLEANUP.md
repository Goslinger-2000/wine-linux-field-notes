# Disk cleanup

## 2026-09-20 (earlier) — lab weight

Removed build/quarantine/tmp and old runtimes (`wine-custom*`, probe prefixes).  
Approx **~14 GiB** freed.

## 2026-09-20 (later) — Wine host path retired

Decision: daily Adobe / CAD / Corel via **WinApps + VM + RAIL** only. Host Wine no longer needed.

### Removed
| Path | ~Was |
|------|------|
| `~/.local/share/wineprefixes/{solidworks,photoshop,coreldraw,ps2024,swlicense}` | ~29 G |
| `~/WineApps/runtimes/*` (`wine-dcomp`, `wine-proton-adobe`, libs, tarball) | ~4 G |
| `~/WineApps/repos/{wine-vanilla,SolidWorks-on-Linux,wine-adobe-installers}` | ~1.2 G |
| `~/WineApps/lab`, `ps2024-qa`, wineapp `*.conf` stubs | small |
| `~/.wine` if present | — |

### Result
- Approx **~33 GiB** freed on `/` (NVMe)
- Free space after: about **86 GiB** (was ~54 GiB)

### Kept
| Path | Why |
|------|-----|
| `~/WineApps/docs` + `public/wine-linux-field-notes` | Field notes (GitHub / GitVerse) |
| `~/WineApps/media` (~1.1 G) | Installers / assets for the Windows guest |
| External WD SSD `WineApps` | Untouched media archive |
| `/opt/wine-staging` | Distro package — remove with `sudo apt remove` if desired |

Reinstall Windows apps only inside the guest; do not recreate host Wine prefixes for PS/SW/Corel.
