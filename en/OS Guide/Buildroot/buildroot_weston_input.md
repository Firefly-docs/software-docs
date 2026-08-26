# Weston Input Devices

Weston service requires at least one input device by default. If there is no input device, special settings in the core section of weston.ini are required:

```poini
# /etc/xdg/weston/weston.ini

[core]
require-input=false
```
