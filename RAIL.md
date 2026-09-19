# FreeRDP + Remmina — RAIL / RemoteApp checklist

Manual checks before or alongside [WinApps](https://github.com/Fmstrat/winapps). Goal: Windows apps as Linux-desktop windows (no full guest wallpaper).

## Clients

| Client | Notes |
|--------|--------|
| **FreeRDP3** | CLI: `xfreerdp3` (lab used **3.31.x**) |
| **Remmina** | GUI; Flatpak `org.remmina.Remmina` or distro `remmina` + RDP plugin |

```bash
xfreerdp3 /version
# Flatpak example:
flatpak run org.remmina.Remmina --version
```

## Full desktop (sanity)

```bash
xfreerdp3 /v:HOST /u:USER /cert:ignore /f
# multi-monitor:
xfreerdp3 /v:HOST /u:USER /cert:ignore /multimon
xfreerdp3 /list:monitor
```

## RAIL / RemoteApp (app window only)

On Windows: enable Remote Desktop; publish RemoteApp **or** let WinApps configure `/app`.

```bash
# RemoteApp alias (often ||ALIAS):
xfreerdp3 /v:HOST /u:USER /cert:ignore /app:program:||PHOTOSHOP

# Or explicit path (example):
xfreerdp3 /v:HOST /u:USER /cert:ignore \
  '/app:program:C:\Program Files\Adobe\Adobe Photoshop 2022\Photoshop.exe'
```

Expected: app chrome on the Linux desktop without the Windows shell desktop.

## Remmina

1. New profile → **RDP**  
2. Server / user / password  
3. Advanced → Remote Application / Start program, or Extra: `/app:program:||ALIAS`  
4. Client resolution; Multi-monitor for full-desktop profiles  

## WinApps

WinApps automates FreeRDP `/app` + `.desktop` launchers. Use this checklist to prove RAIL before wiring every installer.

See also: [VERDICT.md](VERDICT.md).
