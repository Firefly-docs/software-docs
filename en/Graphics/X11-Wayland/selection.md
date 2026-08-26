# Selection Suggestions

- Use X11 first when a full desktop experience, browser, traditional windowed applications, or compatibility with older GUI programs is required.
- Use Wayland/Weston first for embedded full-screen UIs, multimedia display, low-latency display, or lightweight graphics systems.
- Ubuntu/Debian firmware uses X11 by default. Before validating Wayland, confirm that the GPU driver, Mesa, EGL, GBM, and application backend match.
- Buildroot firmware uses Wayland/Weston by default. For application development, prefer embedded display backends such as Wayland, DRM/KMS, or Qt EGLFS.
