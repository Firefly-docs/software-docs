# Common Display Configuration

Weston color format, rotation, resolution, and scaling can be configured in the `[core]` or `[output]` sections of `weston.ini`. In the Buildroot SDK, Weston uses `ARGB8888` by default. Low-performance platforms can switch to `rgb565` when appropriate:

```ini
[core]
gbm-format=rgb565
```

Configure a single display:

```ini
[output]
name=LVDS-1
gbm-format=rgb565
transform=rotate-90
mode=1280x800
scale=2
```

Runtime changes can also be made through the dynamic configuration file:

```bash
echo "output:all:rotate90" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:mode=800x600" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:size=1920x1080" > /tmp/.weston_drm.conf
```

To scale the UI while keeping the physical display mode unchanged, configure the following in `/etc/profile.d/weston.sh`:

```bash
export WESTON_DRM_VIRTUAL_SIZE=1024x768
```

If the hardware VOP display module does not support the required scaling operation, RGA is needed for processing.
