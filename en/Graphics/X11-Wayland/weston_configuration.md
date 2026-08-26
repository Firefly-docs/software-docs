# Buildroot Weston Configuration

Common Weston configuration entry points in the Buildroot SDK are:

| Method | Path or example | Purpose |
| --- | --- | --- |
| Startup arguments | `/etc/init.d/S49weston` | Configure Weston startup arguments, such as `--tty=2`, `--idle-time=0`, and `--warm-up`. |
| weston.ini | `/etc/xdg/weston/weston.ini`, `/etc/xdg/weston/weston.ini.d/*.ini` | Configure common Weston sections such as core, shell, launcher, output, and libinput. |
| Environment variables | `/etc/profile.d/weston.sh` | Configure Rockchip Weston extension variables for multi-display, scaling, AFBC, touchscreen calibration, and similar features. |
| Dynamic configuration file | `/tmp/.weston_drm.conf` | Configure the DRM backend at runtime, such as display on/off, rotation, resolution, freeze, and multi-display position. Use `WESTON_DRM_CONFIG` to specify another path. |
| udev rules | `/lib/udev/rules.d/*.rules` | Bind input devices to displays or configure touchscreen calibration parameters. |

Weston uses the output/head name to identify display devices. Check the Weston startup log for names such as `DSI-1`, `HDMI-A-1`, and `eDP-1`:

```bash
weston --debug
```
