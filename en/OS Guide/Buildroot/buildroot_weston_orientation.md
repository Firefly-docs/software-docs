# Weston Screen Orientation

Weston's screen display direction can be configured in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=LVDS-1
# normal|90|180|270|flipped|flipped-90|flipped-180|flipped-270
transform=180
```

If you need to dynamically configure the screen orientation, you can use a dynamic configuration file, such as:

```bash
echo "output:all:rotate90"> /tmp/.weston_drm.conf # All screens are rotated 90 degrees
echo "output:eDP-1:rotate180"> /tmp/.weston_drm.conf # eDP-1 rotate 180 degrees
```
