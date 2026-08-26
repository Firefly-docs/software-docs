# Weston Standby and Lock Screen

Weston's timeout standby time can be configured in the startup parameters or in the core section of weston.ini, such as:

```bash
# /etc/init.d/S50launcher
     start)
         ...
         # 0 is to prohibit standby, the unit is second
         weston --tty=2 -B=drm-backend.so --idle-time=0&
```

or:

```ini
# /etc/xdg/weston/weston.ini

[core]
# Set to enter the standby state after 5 seconds of inactivity
idle-time=5
```
