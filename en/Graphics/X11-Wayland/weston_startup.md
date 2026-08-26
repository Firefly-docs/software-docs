# Freeze Display and Avoid Black Screen

During Weston startup, a short black screen may appear between the boot logo and the first UI frame. Rockchip Weston extensions can temporarily freeze the display content and unfreeze it after the UI is ready.

Method 1: use the custom `--warm-up` argument so Weston starts showing content after the UI starts:

```bash
/usr/bin/weston --warm-up&
```

Method 2: freeze the display with `WESTON_FREEZE_DISPLAY`:

```bash
export WESTON_FREEZE_DISPLAY=/tmp/.weston_freeze
touch /tmp/.weston_freeze
/usr/bin/weston&
sleep 1 && rm /tmp/.weston_freeze&
```

Method 3: freeze and unfreeze through the DRM dynamic configuration file:

```bash
echo "output:all:freeze" > /tmp/.weston_drm.conf
/usr/bin/weston&
sleep 1 && echo "output:all:unfreeze" > /tmp/.weston_drm.conf&
```

Adjust the freeze time according to the actual UI startup time. A delay that is too short may still show a black screen, while a delay that is too long slows visible startup.
