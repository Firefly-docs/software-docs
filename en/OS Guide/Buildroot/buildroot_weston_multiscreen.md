# Weston Multi-screen

The Weston of the Buildroot SDK supports functions such as multi-screen same-different display and hot-plugging. The distinction between different display screens is based on the drm name (obtained via /sys/class/drm/card0-name), and the relevant configuration is set through environment variables, such as:

```bash
# /etc/init.d/S50launcher

    start)
        ...
        export WESTON_DRM_PRIMARY=HDMI-A-1 # Specify the main display as HDMI-A-1
        export WESTON_DRM_MIRROR=1 # Use mirror mode (multiple screens at the same display), if you don’t set this environment variable, it will be a different display
        export WESTON_DRM_KEEP_RATIO=1 # The zoom maintains the aspect ratio in the mirror mode. If this variable is not set, the full screen is forced
        export WESTON_DRM_PREFER_EXTERNAL=1 # The built-in display is automatically turned off when the external display is connected
        export WESTON_DRM_PREFER_EXTERNAL_DUAL=1 # When the external display is connected, the first external display is the main display by default
        weston --tty=2 -B=drm-backend.so --idle-time=0&
```

When zooming the display content in the mirror mode, you need to rely on RGA acceleration.

At the same time, it is also supported to individually disable specific screens in the output section of weston.ini:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=LVDS-1
mode=off
# off|current|preferred|<WIDTHxHEIGHT@RATE>
```
