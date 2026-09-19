# Wine + Adobe / CAD on Linux — field notes

Informal notes from running **Photoshop 2024**, related Adobe UI, and adjacent Windows apps under Wine on **Linux Mint / Cinnamon** (NVIDIA RTX 4060 Ti, dual monitors) — and the decision to move daily work to **WinApps + VM + RAIL**.

> **AI notice:** Investigation and wording were largely produced with an **AI coding agent (Cursor)**; a human validated via screenshots and interactive use. Treat claims as **unverified until reproduced**. No warranty. Not affiliated with Adobe, Dassault, Corel, CodeWeavers, WineHQ, DXVK, or FreeRDP.

## Final verdict

**Use WinApps (Windows VM) + FreeRDP RemoteApp / RAIL for calm daily Photoshop, CorelDRAW, and SolidWorks. Keep Wine as a research / bug-report path only.**

→ Full write-up: **[VERDICT.md](VERDICT.md)** · Русское резюме: **[SUMMARY.ru.md](SUMMARY.ru.md)**

## Documents

| File | Language | Content |
|------|----------|---------|
| [VERDICT.md](VERDICT.md) | EN | **Canonical decision** + app version guidance |
| [SUMMARY.ru.md](SUMMARY.ru.md) | RU | Итоги стека, anti-patterns, WinApps |
| [NOTES.en.md](NOTES.en.md) | EN | Full Photoshop 2024 Wine field notes |
| [RAIL.md](RAIL.md) | EN | FreeRDP3 / Remmina RemoteApp checklist |
| [UPSTREAM.md](UPSTREAM.md) | EN | Gist, DXVK, WineHQ, mirror URLs |
| [CLEANUP.md](CLEANUP.md) | RU/EN | What we kept vs deleted when freeing disk |

## One-screen takeaway (Photoshop 2024 on Wine)

| Worked here | Fragile / failed |
|-------------|------------------|
| Menu with `Managed=N` + `Decorated=N` | `Managed=Y` ↔ WM frame vs Adobe menu |
| GPU via DXVK + NVIDIA ICD | WineD3D → Unknown GPU / 0 VRAM |
| Canvas if `d2d1` has **no** `D2D1ComputeMaximumScaleFactor` | That export ↔ black voids (Wine **58913**) |
| Open file after idle: Ctrl+O + **unmap** Progress | CLI cold-open hangs (`CreateSwapChainForComposition`) |
| Workspace switch **before** launch only | Unmap/Map main window → black voids |

## Decision (short)

| Role | Choice |
|------|--------|
| Daily Adobe / Corel / SolidWorks | **WinApps + VM + RAIL** (FreeRDP3 / Remmina) |
| Guest apps (no GPU yet) | PS **23.5.x** + ACR **≤14.5**; Corel OK; SW light only |
| Later | GPU passthrough or Windows mini-PC |
| Linux host | Games + AI swarm keep the discrete GPU |
| Wine | Reference / upstream reports only |

## License

[CC0-1.0](LICENSE) — public domain dedication.
