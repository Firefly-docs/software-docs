# Weston Resolution and Scaling

The screen resolution and scaling of Weston can be configured in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=HDMI-A-1
# Need to be an effective resolution supported by the screen
mode=1920x1080
# Must be an integer multiple
scale=2
```

If you need to dynamically configure resolution and scaling, you can use dynamic configuration files, such as:

```bash
echo "output:HDMI-A-1:mode=800x600"> /tmp/.weston_drm.conf # Modify the resolution of HDMI-A-1 to 800x600
```

You need to rely on RGA acceleration when scaling in this way.
