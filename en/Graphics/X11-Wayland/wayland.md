# Wayland

Wayland is a newer Linux display protocol designed as an alternative to the traditional X11 architecture. In a Wayland stack, the compositor acts as the display server. Applications communicate with the compositor through the Wayland protocol, and the compositor handles window composition, input dispatch, and display output.

Wayland is suitable for modern graphics stacks, embedded UIs, low-latency display, and a more direct DRM/KMS display path. It usually works together with EGL, GBM, DRM/KMS, and the GPU driver.

Check whether the current session is Wayland:

```bash
echo $XDG_SESSION_TYPE
```

If the output is `wayland`, the current session is running on Wayland.

You can also check the Wayland display environment variable:

```bash
echo $WAYLAND_DISPLAY
```
