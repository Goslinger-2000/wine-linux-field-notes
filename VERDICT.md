# Verdict: Wine experiments → WinApps + VM + RAIL

**Date:** 2026-09  
**Lab:** Linux Mint / Cinnamon, Ryzen 5 5600, ~62 GB RAM, single RTX 4060 Ti, dual monitors  
**Disclaimer:** AI-assisted field notes (Cursor); human-validated where noted. Reproduce before trusting. No warranty. Not affiliated with Adobe, Dassault, Corel, CodeWeavers, WineHQ, DXVK, or FreeRDP.

---

## One-line verdict

**Wine mapped the ceilings; daily Adobe / CAD / vector work should run in a Windows VM and reach the Linux desktop via WinApps (FreeRDP RemoteApp / RAIL), not as a primary Wine workload.**

---

## What Wine taught us (Photoshop 2024)

Worth keeping as reference — not as the daily driver:

| Area | Outcome |
|------|---------|
| Adobe menu vs WM frame | `Managed=N` + `Decorated=N` works; `Managed=Y` brings Cinnamon chrome |
| GPU recognition | Needs DXVK + NVIDIA ICD; WineD3D → Unknown GPU |
| Black voids | Correlated with `D2D1ComputeMaximumScaleFactor` in `d2d1` (Wine **58913**) and with Unmap/Map of the main window |
| Progress hang | Cold CLI open + `CreateSwapChainForComposition`; workaround: idle → Ctrl+O → **unmap** Progress (never Destroy) |
| Workspace pin via Unmap | Anti-pattern — destroys DComp/DXVK surfaces |

Full detail: [NOTES.en.md](NOTES.en.md), [SUMMARY.ru.md](SUMMARY.ru.md).

---

## Recommended production path

```
Linux host (games + AI swarm keep the dGPU)
    └── Windows guest (KVM / WinApps)
            ├── Photoshop, Camera Raw, CorelDRAW, SolidWorks (as needed)
            └── Exposed to desktop via FreeRDP3 / Remmina  →  RAIL / RemoteApp
```

| Phase | Stack |
|-------|--------|
| **Now** | WinApps + Windows VM + FreeRDP3 RAIL (`/app`) |
| **Later (optional)** | Same VM with **GPU passthrough** (e.g. spare 1650 Super) for SolidWorks viewport / heavy ACR |
| **Alt** | Dedicated Windows mini-PC (e.g. 10 LTSC) + FreeRDP from Linux |

**Wine** stays for light experiments and upstream bug reports only.

---

## App guidance under WinApps (no guest GPU yet)

| App | Guidance |
|-----|----------|
| **Photoshop** | Prefer **2022 `23.5.x`** (e.g. `23.5.0`–`23.5.5`) with **Camera Raw ≤14.5**. ACR 15+ often demands a real GPU for editing. HDR Merge / classic RAW tools are fine on ACR 14 without generative features. |
| **Camera Raw** | Set **Use Graphics Processor → Off** if the guest has only Basic/QXL/virtio display. |
| **CorelDRAW** | Comfortable on WinApps even without passthrough (vector/layout). |
| **SolidWorks** | OK for light 2D / small parts; **3D assemblies need GPU in the guest** (passthrough or mini-PC). |

Clients used for RAIL tests: **FreeRDP 3.x** (`xfreerdp3`) and **Remmina** (Flatpak or distro). See [RAIL.md](RAIL.md).

---

## Why not “just finish Wine”?

1. DComp / composition swapchains are a structural mismatch with DXVK.  
2. Override-redirect vs Cinnamon panel/frame has no clean both-sides fix.  
3. Each Adobe / CAD release re-breaks the same edges.  
4. A legal Windows guest + RAIL reuses real GPU drivers (when passed through) and real installers without fighting X11 for every dialog.

---

## Upstream mirrors

- This repo (GitHub / GitVerse — see [UPSTREAM.md](UPSTREAM.md))  
- Gist: https://gist.github.com/Goslinger-2000/bbc213da8c93c1204a00b035553893cd  
- DXVK / WineHQ comments listed in UPSTREAM.md  

---

## License

[CC0-1.0](LICENSE)
