# GPU Troubleshooting

## glmark2 Fails to Start

If `glmark2` or `glmark2-es2` fails to start, first check the current graphics environment:

```bash
echo $DISPLAY
echo $WAYLAND_DISPLAY
```

In an X11 environment, a valid `DISPLAY` is usually required. In Wayland or DRM/KMS environments, use a matching EGL backend or the proper test tool options.

## Performance Is Low or Software Rendering Is Used

If `glmark2-es2` shows `llvmpipe`, the system is using CPU software rendering. Common causes include:

- The GPU driver is not loaded.
- The userspace GPU library does not match the kernel driver.
- The graphics session is not using the correct EGL/DRI driver.
- Remote desktop, virtual display, or incorrect environment variables caused rendering fallback.

Validate again on a local HDMI/DP display first to rule out remote desktop or virtual display effects.
