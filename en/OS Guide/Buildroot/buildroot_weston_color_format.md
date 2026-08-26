# Weston Display Color Format

The current default display format of Weston in Buildroot SDK is ARGB8888. For some low-performance platforms, it can be configured as RGB565 in the core section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[core]
# xrgb8888|rgb565|xrgb2101010
gbm-format=rgb565
```

You can also configure the display format of each screen separately in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
# The name of output can be viewed /sys/class/drm/card0-name
name=LVDS-1
# xrgb8888|rgb565|xrgb2101010
gbm-format=rgb565
```
