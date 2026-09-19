# Итоги: Wine → WinApps + VM + RAIL

## Вердикт (одно предложение)

Эксперименты с Wine **закрыли карту потолков**; для спокойной ежедневной работы Adobe / CAD / Corel — **Windows в VM + WinApps (FreeRDP RemoteApp / RAIL)**, а не Wine как основной путь.

Подробно по-английски: [VERDICT.md](VERDICT.md).

## Стенд
- Linux Mint / Cinnamon, Ryzen 5 5600, ~62 ГБ RAM, **одна** RTX 4060 Ti (без iGPU)
- Два монитора: портрет HDMI + основной DP
- Runtime экспериментов: experimental **wine-dcomp** + **DXVK**
- Префиксы: `ps2024`, `photoshop`, `solidworks`, `coreldraw` (держать до рабочего WinApps, потом можно снести)

## Что выяснили (PS 2024 / Wine)
1. Меню Adobe ↔ `Managed=N` + `Decorated=N` (override-redirect). На Cinnamon иначе часто рамка WM.
2. GPU ↔ DXVK + NVIDIA ICD; без DXVK — Unknown GPU.
3. Чёрные дыры ↔ экспорт `D2D1ComputeMaximumScaleFactor` в d2d1 (Wine **58913**) и/или unmap главного окна.
4. «Ход выполнения» ↔ CLI open файла + stub `CreateSwapChainForComposition`; обход: idle → Ctrl+O → **unmap** Progress (**не** Destroy).
5. Workspace pin через Unmap — anti-pattern (ломает DComp/DXVK).

## Решение по стеку

| Этап | Что |
|------|-----|
| **Сейчас** | WinApps + Windows VM + FreeRDP3 / Remmina (RAIL) |
| **Потом** | GPU passthrough (например 1650 Super) в guest — в первую очередь SolidWorks |
| **Альтернатива** | Отдельный Win mini-PC (10 LTSC) + FreeRDP с Linux |
| **Хост Linux** | Игры + ИИ-рой оставляют дискретную GPU |
| **Wine** | Справка / апстрим-баги, не daily driver |

Клиенты RAIL: см. [RAIL.md](RAIL.md).

## Приложения в guest (без passthrough)

| Приложение | Рекомендация |
|------------|--------------|
| **Photoshop** | **2022, билд `23.5.x`** (норм и `23.5.0.669`, и `23.5.1`, и финал `23.5.5`) + **Camera Raw ≤14.5** |
| **Camera Raw** | HDR Merge / классика — да; генерация не нужна. GPU в ACR → **Off**, если в VM нет нормальной карты. ACR **15+** часто требует GPU для редактирования |
| **CorelDRAW** | Спокойно идёт под WinApps без GPU |
| **SolidWorks** | Лёгкое/2D — ок; серьёзный 3D viewport — только с GPU в guest |

## Anti-patterns (Wine)
- Unmap/Map главного окна Photoshop ради вирт. столов  
- XDestroyWindow на Progress  
- Мешать Wine `dxgi` (dcomp) и DXVK `d3d11`  
- Кастомные «вырезаные» Windows ISO ради телеметрии на рабочей станции  

## Апстрим
См. [UPSTREAM.md](UPSTREAM.md).

## Лицензия
[CC0-1.0](LICENSE) — без гарантий; AI-assisted, проверяйте сами.
