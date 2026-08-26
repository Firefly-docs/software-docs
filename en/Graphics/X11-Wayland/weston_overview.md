# Weston

Weston is the reference compositor for the Wayland protocol and is commonly used as a Wayland display server implementation in embedded Linux. It can output directly through DRM/KMS and can be used as a desktop shell, full-screen shell, or kiosk-style graphics environment.

In embedded desktop systems built with Buildroot or Yocto, the common stack is:

```text
Weston + Wayland
```

Rockchip Buildroot SDK uses the Weston DRM backend as the default display service. Weston applications can provide basic demo functions such as a panel, background, Chromium browser, terminal, launchers, camera preview, multi-video display, GPU demos, and mouse demos. The system usually also includes Weston client test programs, for example:

```bash
weston-simple-egl
weston-simple-shm
weston-simple-touch
weston-screenshooter
weston-terminal
weston-touch-calibrator
```

Weston is suitable for:

- Embedded full-screen UIs.
- Multimedia playback and video display.
- Applications using Wayland backends, such as Qt, GTK, and GStreamer.
- Product systems that do not require a full traditional desktop environment.

Show Weston service status:

```bash
systemctl status weston
```

Show the Weston version:

```bash
weston --version
```
