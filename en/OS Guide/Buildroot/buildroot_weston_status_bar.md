# Weston Status Bar

Weston supports setting the background color and position of the status bar in the shell section of the weston.ini configuration file, as well as setting quick launch programs in the launcher section, such as:

```ini
# /etc/xdg/weston/weston.ini

[shell]
# The color format is ARGB8888
panel-color=0xff002244
# top|bottom|left|right|none
panel-position=bottom

[launcher]
icon=/usr/share/weston/terminal.png
path=/usr/bin/weston-terminal

[launcher]
# Icon path
icon=/usr/share/weston/icon_flower.png
# Quick start command
path=/usr/bin/qsetting
```
