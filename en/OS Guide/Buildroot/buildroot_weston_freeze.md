# Weston Freeze Screen

When Weston was started, there was a black screen for a short period switching between the boot logo and the UI display. If you need to prevent a black screen, you can temporarily freeze the Weston screen content through the following dynamic configuration file methods:

```bash
# /etc/init.d/S50launcher
     start)
         ...
         export WESTON_FREEZE_DISPLAY=/tmp/.weston_freeze # Set the path of the special configuration file
         touch /tmp/.weston_freeze # Freeze display
         weston --tty=2 -B=drm-backend.so --idle-time=0&
         ...
         sleep 1 && rm /tmp/.weston_freeze& # Thaw in 1 second
```
