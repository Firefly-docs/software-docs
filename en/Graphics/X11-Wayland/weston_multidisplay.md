# Display State and Multi-Display Management

Weston in the Buildroot SDK supports mirrored display, extended display, display position configuration, and hotplug. Multi-display policy can be configured in `/etc/profile.d/weston.sh`:

```bash
export WESTON_DRM_PRIMARY=HDMI-A-1
export WESTON_DRM_SINGLE_HEAD=1
export WESTON_DRM_MIRROR=1
export WESTON_DRM_KEEP_RATIO=1
export WESTON_DRM_HEAD_MODE=external-dual
export WESTON_DRM_HEAD_FALLBACK=1
export WESTON_OUTPUT_FLOW=horizontal
```

Display state and position can also be adjusted through the dynamic configuration file:

```bash
echo "output:DSI-1:off" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:freeze" > /tmp/.weston_drm.conf
echo "output:eDP-1:on" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:pos=100,200" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:primary" > /tmp/.weston_drm.conf
```

The DRM connector state can be forced through sysfs:

```bash
echo on > /sys/class/drm/card0-HDMI-A-1/status
```

Common `status` values are `on`, `off`, and `detect`. `detect` follows the hotplug state.
