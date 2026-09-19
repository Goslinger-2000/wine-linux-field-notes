# Wine + Adobe / CAD on Linux — field notes

Informal notes from running **Photoshop 2024**, related Adobe UI stack, and adjacent Windows apps under Wine on **Linux Mint / Cinnamon** (NVIDIA RTX 4060 Ti, dual monitors).

> **AI notice:** Much of the investigation and wording was produced with an **AI coding agent (Cursor)**; a human validated via screenshots and interactive use. Treat claims as **unverified until reproduced**. No warranty. Not affiliated with Adobe, CodeWeavers, WineHQ, or DXVK.

## Why this exists

We pushed Wine far enough to map **real ceilings** (DComp / DXVK / Managed windows), then chose **WinApps / a Windows mini-PC** for daily Photoshop & SolidWorks. These notes remain so others (and future us) do not repeat the same week of pitfalls.

## Documents

| File | Language | Content |
|------|----------|---------|
| [NOTES.en.md](NOTES.en.md) | EN | Full Photoshop 2024 field notes |
| [SUMMARY.ru.md](SUMMARY.ru.md) | RU | Итоги стека, anti-patterns, решение |
| [UPSTREAM.md](UPSTREAM.md) | EN | Links we posted (gist, DXVK, WineHQ) |
| [CLEANUP.md](CLEANUP.md) | RU/EN | What we kept vs deleted when freeing disk |

## One-screen takeaway (Photoshop 2024)

| Worked here | Fragile / failed |
|-------------|------------------|
| Menu with `Managed=N` + `Decorated=N` | `Managed=Y` ↔ WM frame vs Adobe menu |
| GPU via DXVK + NVIDIA ICD | WineD3D → Unknown GPU / 0 VRAM |
| Canvas if `d2d1` has **no** `D2D1ComputeMaximumScaleFactor` | That export ↔ black voids (Wine **58913**) |
| Open file after idle: Ctrl+O + **unmap** Progress | CLI cold-open hangs (`CreateSwapChainForComposition`) |
| Workspace switch **before** launch only | Unmap/Map main window → black voids |

## Decision

- **Daily Adobe / SolidWorks:** WinApps (VM) now → later Windows mini-PC + FreeRDP.  
- **Linux host:** games + AI swarm keep the discrete GPU.  
- **Wine:** kept as reference / light experiments, not primary PS path.

## License

[CC0-1.0](LICENSE) — public domain dedication.
