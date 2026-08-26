# GPU Driver Environment

The GPU handles graphics rendering, window composition, 3D acceleration, general-purpose compute, and some browser WebGL workloads. On Firefly Linux systems, common GPU capabilities include OpenGL, Vulkan, OpenGL ES, OpenCL, and browser hardware acceleration.

This document describes the GPU driver environment, OpenGL/Vulkan, OpenGL ES, OpenCL verification, and common troubleshooting steps. Available features may vary by chip, kernel version, system image, and driver package. Always use the actual SDK, firmware, and board specification as the final reference.

Common GPU driver stacks on Rockchip platforms include:

- Official Mali DDK userspace driver: usually preinstalled in official Firefly firmware and suitable for stable product images.
- Mesa Panfrost / Panthor open-source driver: suitable for validating open-source OpenGL and Vulkan support on supported kernels and Mesa versions.

In the Kernel 6.1 SDK, RK3588 and RK3576 can use the Mesa 25.0.7 Panthor / Panfrost driver stack. Before switching driver stacks, confirm the current system image, kernel version, Mesa version, and product requirements.

Check GPU-related system information:

```bash
uname -a
lsmod | grep -E "mali|panfrost|panthor|drm"
dmesg | grep -Ei "mali|panfrost|panthor|gpu"
```
