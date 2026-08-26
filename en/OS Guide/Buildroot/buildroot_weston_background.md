# Weston Background

Weston supports setting background patterns and colors in the shell section of the weston.ini configuration file, such as:

```ini
# /etc/xdg/weston/weston.ini

[shell]
# Background pattern (wallpaper) absolute path
background-image=/usr/share/weston/background.png
# scale|scale-crop|tile
background-type=scale
# The color format is ARGB8888, which will take effect when the background pattern is not set
background-color=0xff002244
```
