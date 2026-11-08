# TCL 50P731X1 (G03 / BeyondTV4, Google TV 11), Netherlands

Tested on a real 50P731X1 on 2026-11-08: `ro.product.model` `Smart TV Pro`, product `G03_4K_GB`, device `BeyondTV4`,
Android 11, build `RP1A.200622.001` / `AR11`, security patch 2024-12-05, client `TCL-EU-RTD51M-S1`, 2 GB RAM, Ethernet.
It is the same platform as `examples/tcl-43c645` and `examples/tcl-g03-google-tv`.

## Files

| File | What it does |
|---|---|
| `debloat.txt` | Groups 1-3 (telemetry, promos, extras, unused preloads), 4 (setupwraith) and 5 (dreamx); header has the probe values and what worked |
| `config.json` | Launcher overlay without wallpaper keys or photos: dock Plex, YouTube, NPO Start; Netflix and Prime buttons, gear opens Settings |
| `tv.json` | Row for `examples/README.md` |

## What to know

- Run group 4 (setupwraith) before `launcher\build-and-install.ps1`, otherwise Home goes to setupwraith's recovery screen and the script rolls back.
  Check `dumpsys wifi | grep -o 'cname=[^ ]*'` first: a Wi-Fi network saved at first setup is deleted when setupwraith goes off.
- Remote is infrared with only Netflix (4062) and Prime Video (4099) buttons; the gear sends 4077. No key helper beyond `HomeKeyService` was needed.
- At standby the firmware stops Plex, HBO Max and other apps on `standbyProcessBlackList` in `/vendor/etc/default_feature_config.json`
  (`TclPowerManagerService.killBlackListStandbyProcess`), plus `com.tcl.ui_mediaCenter`, `gallery`, `dashboard` and `partnercustomizer`.
  YouTube and NPO Start resume, HDMI stays on HDMI. The file cannot be changed without root.
- `pm install-multiple` does not exist on this firmware. A split APK (APKMirror `.apkm`) installs with `pm install-create`, `install-write` per
  split and `install-commit`.
- Plex 10.30.8.4222 (older than the preinstalled 2026.20.0) installed after `pm uninstall`; it is signed with the same certificate.

```powershell
tools\disable.ps1 -TvHost <tv ip> <group list>
launcher\build-and-install.ps1 -TvHost <tv ip> -HomeKeyService
tools\theme.ps1 -TvHost <tv ip> examples\tcl-50p731x1
tools\restore.ps1 -TvHost <tv ip> examples\tcl-50p731x1\debloat.txt -Stock com.google.android.apps.tv.launcherx
```
