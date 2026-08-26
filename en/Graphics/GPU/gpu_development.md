# GPU Development Suggestions

- For product firmware, use a verified driver combination and avoid mixing GPU libraries from different SDKs.
- GUI applications should clearly choose their target backend, such as X11, Wayland, DRM/KMS, or Qt EGLFS.
- Vulkan and OpenCL workloads should be stress-tested with the target application load before release.
- Browser WebGL scenarios should verify the Chromium version, GPU blacklist, window system, and video composition path together.
- After updating the kernel, Mesa, Mali DDK, or root filesystem, re-run OpenGL, OpenGL ES, Vulkan, and OpenCL verification.
