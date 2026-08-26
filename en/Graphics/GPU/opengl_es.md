# OpenGL ES

OpenGL ES is a graphics API for embedded devices. It is commonly used by Qt EGLFS, Wayland/Weston, GStreamer display paths, camera preview, industrial UI, browser WebGL, and lightweight 3D applications.

OpenGL ES support depends on the chip GPU model and driver version. Use the actual firmware and driver output as the final result:

```bash
glmark2-es2
```

If these tools are not available, install them:

```bash
sudo apt update
sudo apt install glmark2-es2
```

Run the OpenGL ES test:

```bash
glmark2-es2
```

Focus on the following checks:

- Whether windowed or full-screen display works correctly.
- Whether EGL initialization succeeds.
- Whether rendering uses the GPU instead of software rendering.
- Whether the workload remains stable during long runs.

## Browser WebGL Verification

Chromium provides a GPU status page:

```text
chrome://gpu
```

If WebGL, WebGL2, or Rasterization is shown as hardware accelerated, the browser graphics acceleration path is basically available. You can also open WebGL test pages to verify real rendering behavior.
