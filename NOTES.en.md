# Photoshop 2024 on Wine (Linux): field notes

**Status:** informal field notes, not an official guide.  
**Disclaimer:** This write-up was largely produced and iterated by an **AI coding agent** (Cursor), with a human operator validating screenshots and UX. Treat every claim as **unverified until you reproduce it**. Configurations, Wine builds, DXVK versions, GPUs, and desktop environments differ; your results will vary. Do **not** treat this as a promise that Photoshop “just works” on Linux.

**Scope:** Adobe Photoshop **2024** (x64) under Wine on **Linux Mint / Cinnamon**, NVIDIA GPU, dual-monitor. Runtime used: experimental **wine-dcomp** (D2D/DComp/DWrite lineage, e.g. community builds such as giang17’s patch sets) **plus DXVK** for GPU.

**Not included:** installers for pirated/portable builds, license bypasses, or redistributed Adobe binaries.

---

## What worked (with caveats)

| Goal | Approach that worked here |
|------|---------------------------|
| Adobe File/Edit menu visible | Wine `HKCU\Software\Wine\X11 Driver`: `Decorated=N`, `Managed=N` (override-redirect). On Cinnamon, `Managed=Y` often brings a WM frame and can break the Adobe chrome tradeoff. |
| GPU / VRAM recognition | DXVK (`dxgi`/`d3d11`) + NVIDIA ICD (`VK_ICD_FILENAMES=…/nvidia_icd.json`). WineD3D alone showed “Unknown GPU” / 0 VRAM in this setup. |
| Avoid black void / broken shell | Ensure Wine `d2d1` does **not** export `D2D1ComputeMaximumScaleFactor` (see Wine bug **58913**). Presence of that export correlated with black voids / broken UI; removing it restored canvas. |
| Launch on a specific workspace | Switch desktop **before** launch (`wmctrl -s N`). Do **not** XUnmap/XMap the main Photoshop window to fake workspace pinning. |
| Open a PNG after shell is up | Keep process CLI **without** a file argument. After the main window is idle, open via UI (e.g. Ctrl+O). If «Ход выполнения» / Progress sticks, **XUnmap** that dialog (and large PlugPlug dummy windows if needed). |

## What failed or is fragile

1. **`CreateSwapChainForComposition` + DXVK**  
   Cold-open of a file via CLI (or very early open) often hangs on the progress dialog. Logs point at DXGI composition swapchain stubs. This matches known DXVK limitations (DirectComposition / composition swapchains not really supported for desktop apps). Dummy composition swapchain env vars may not fix Photoshop. Mixing Wine builtin `dxgi` (dcomp patches) with DXVK `d3d11` is unsafe (device/COM mismatch).

2. **Workspace “pin” via Unmap/Map**  
   Unmapping override-redirect Photoshop windows to hide them on other virtual desktops **destroyed** DComp/DXVK surfaces → persistent **black voids**. Anti-pattern: do not unmap the main UI for workspace management.

3. **XDestroyWindow on the progress dialog**  
   Observed hard Wine deadlock (`RtlpWaitForCriticalSection`). Prefer **unmap**, never destroy, if you must clear a stuck Progress window.

4. **Panel icon vs no frame**  
   `Managed=N` → weak/unstable taskbar integration on Cinnamon. `Managed=Y` → better panel behavior, but WM chrome/frame returns. No clean “both” found here.

5. **Win32 theme / scrollbar colors**  
   Registry `ThemeManager` / `Control Panel\Colors` only affects classic Win32 controls. AdobeOwl-drawn UI often ignores them; document scrollbars may still look like Windows classic under Wine.

6. **Edge WebView / PlugPlug dummy windows**  
   Large blackish rectangles can be leftover webview/host windows; unmapping **only** those titled like `PlugPlugDummyWindow` helped visually. Do not unmap the main document window.

## Suggested stack constraints (checklist)

- [ ] wine-dcomp (or equivalent) for D2D/UI path  
- [ ] DXVK for GPU **or** accept Wine D3D / no GPU — do not casually mix `dxgi` implementations  
- [ ] Confirm `d2d1` has **no** `D2D1ComputeMaximumScaleFactor` export if UI goes black  
- [ ] `Decorated=N` + `Managed=N` if Adobe menu matters more than WM chrome  
- [ ] Empty file argv on launch; open files after UI idle  
- [ ] Never workspace-pin via Unmap of the main window  
- [ ] Never Destroy stuck Progress; Unmap only  

## Upstream references (please verify current status)

- Wine Bugzilla **58913** (D2D / UI scale factor / related UI breakage — confirm title and status yourself)  
- DXVK issues discussing `CreateSwapChainForComposition: Not implemented` (e.g. community reports around composition swapchains / DComp; DXVK maintainers have stated DComp is largely out of scope without Wine work)  
- Community wine-dcomp / D2D+DComp patch documentation (e.g. patch notes warning that DXVK replaces Wine `dxgi` and bypasses composition patches)

## Repro sketches (for developers)

**A. Black UI after enabling ComputeMaximum export**  
1. Use a Wine build where `d2d1` exports `D2D1ComputeMaximumScaleFactor`.  
2. Launch Photoshop 2024 with GPU path.  
3. Observe black void / incomplete shell.  
4. Rebuild/replace `d2d1` without that export; relaunch — compare.

**B. Progress hang on file open with DXVK**  
1. `wine Photoshop.exe C:\some.png` (or equivalent) under DXVK.  
2. Progress dialog may never finish; composition swapchain errors in log.  
3. Same session: File → New may still produce a GPU canvas.  
4. After idle shell, open via Ctrl+O; if Progress sticks, Unmap (not Destroy) and check whether the document appears.

## AI / verification notice (please read)

- Authored primarily by an **AI agent**; human spot-checked via screenshots and interactive use.  
- **Needs independent reproduction** before citing as fact in upstream bugs.  
- Dates and build hashes were environment-specific (Wine custom build, DXVK 2.x, NVIDIA driver of the day, Cinnamon).  
- This is **not** affiliation with Adobe, CodeWeavers, or DXVK maintainers.

## License

CC0-1.0 (or treat as public domain notes). No warranty.
