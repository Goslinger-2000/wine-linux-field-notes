# Итоги: Wine-среда (Photoshop 2024 и рядом)

## Стенд
- Linux Mint / Cinnamon, Ryzen 5 5600, ~62 ГБ RAM, **одна** RTX 4060 Ti (без iGPU)
- Два монитора: портрет HDMI + основной DP
- Runtime: experimental **wine-dcomp** + **DXVK**
- Префикс экспериментов: `ps2024` (и отдельно photoshop / solidworks / coreldraw)

## Что выяснили (PS2024)
1. Меню Adobe ↔ `Managed=N` + `Decorated=N` (override-redirect). На Cinnamon иначе часто рамка WM.
2. GPU ↔ DXVK + NVIDIA ICD; без DXVK — Unknown GPU.
3. Чёрные дыры ↔ экспорт `D2D1ComputeMaximumScaleFactor` в d2d1 (Wine **58913**) и/или unmap главного окна.
4. «Ход выполнения» ↔ CLI open файла + stub `CreateSwapChainForComposition`; обход: idle → Ctrl+O → **unmap** Progress (**не** Destroy).
5. Workspace pin через Unmap — anti-pattern (ломает DComp/DXVK).

## Решение
- **Сейчас:** WinApps (Windows в VM), GPU остаётся Linux (игры + ИИ-рой).
- **Потом:** отдельный Win mini-PC (10 LTSC) + FreeRDP; копить на железо.
- Wine PS — не основной путь; заметки выложены в этот репозиторий / gist.

## Anti-patterns
- Unmap/Map главного окна Photoshop ради вирт. столов  
- XDestroyWindow на Progress  
- Мешать Wine `dxgi` (dcomp) и DXVK `d3d11`  
- Кастомные «вырезаные» Windows ISO ради телеметрии на рабочей станции  

## Апстрим
См. [UPSTREAM.md](UPSTREAM.md).
