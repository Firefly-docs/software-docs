# GPU

The GPU handles graphics rendering, window composition, 3D acceleration, general-purpose compute, and some browser WebGL workloads. On Firefly Linux systems, common GPU capabilities include OpenGL, Vulkan, OpenGL ES, OpenCL, and browser hardware acceleration.

This document describes the GPU driver environment, OpenGL/Vulkan, OpenGL ES, OpenCL verification, and common troubleshooting steps. Available features may vary by chip, kernel version, system image, and driver package. Always use the actual SDK, firmware, and board specification as the final reference.

## GPU Driver Environment

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

## OpenGL and Vulkan

OpenGL and Vulkan are used for desktop graphics, 3D rendering, visualization interfaces, games, and graphics benchmarks. OpenGL is a traditional graphics API, while Vulkan provides a modern low-overhead API with more explicit control.

### Install the Panfrost / Panthor Driver

On supported system images, use the Firefly script to install the Panfrost / Panthor driver packages:

```bash
sudo panfrost.sh
sudo reboot
```

After rebooting, check whether the driver is loaded:

```bash
dmesg | grep -Ei "panfrost|panthor"
```

If GPU driver initialization messages are present, the kernel-side driver has been detected. Userspace OpenGL / Vulkan support should still be verified with tools such as `glxinfo`, `glmark2`, and `vulkaninfo`.

### OpenGL Verification

Install test tools:

```bash
sudo apt update
sudo apt install glmark2
```

Run the OpenGL benchmark:

```bash
glmark2
```

Key checks:

- Whether `OpenGL renderer string` in `glxinfo -B` shows the expected GPU driver.
- Whether `glmark2` can open a window and finish the test.
- Whether the test shows display corruption, hangs, window creation failures, or fallback to software rendering.

### Vulkan Verification

Install Vulkan tools:

```bash
sudo apt install vulkan-tools
```

Show Vulkan information:

```bash
vulkaninfo --summary
```

Run the basic sample:

```bash
vkcube
```

If `vkcube` displays a rotating cube, the basic Vulkan rendering path is working. If it reports a missing ICD, instance creation failure, or unavailable device, check whether the Vulkan driver package, ICD configuration, and current GPU driver stack match each other.

## OpenGL ES

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

### Browser WebGL Verification

Chromium provides a GPU status page:

```text
chrome://gpu
```

If WebGL, WebGL2, or Rasterization is shown as hardware accelerated, the browser graphics acceleration path is basically available. You can also open WebGL test pages to verify real rendering behavior.

## OpenCL

OpenCL is used for general-purpose GPU computing, including image processing, parallel computing, preprocessing, postprocessing, and some AI-related helper workloads. OpenCL availability depends on the chip GPU, driver, and userspace runtime.

Install and check OpenCL information:

```bash
sudo apt update
sudo apt install clinfo
clinfo
```

Key checks:

- Whether an OpenCL Platform is detected.
- Whether a GPU Device is detected.
- Whether `Device Type` includes `GPU`.
- Whether the OpenCL C version, maximum work-group size, and global memory size meet the application requirements.

If `clinfo` cannot find a platform or device, check:

- Whether the OpenCL userspace library is installed.
- Whether the ICD configuration exists and points to the correct driver library.
- Whether the current firmware includes OpenCL support for the GPU.
- Whether mismatched Mali DDK, Mesa, kernel driver, or root filesystem components are mixed together.

## Troubleshooting

### glmark2 Fails to Start

If `glmark2` or `glmark2-es2` fails to start, first check the current graphics environment:

```bash
echo $DISPLAY
echo $WAYLAND_DISPLAY
```

In an X11 environment, a valid `DISPLAY` is usually required. In Wayland or DRM/KMS environments, use a matching EGL backend or the proper test tool options.

### Performance Is Low or Software Rendering Is Used

If `glmark2-es2` shows `llvmpipe`, the system is using CPU software rendering. Common causes include:

- The GPU driver is not loaded.
- The userspace GPU library does not match the kernel driver.
- The graphics session is not using the correct EGL/DRI driver.
- Remote desktop, virtual display, or incorrect environment variables caused rendering fallback.

Validate again on a local HDMI/DP display first to rule out remote desktop or virtual display effects.

## Development Suggestions

- For product firmware, use a verified driver combination and avoid mixing GPU libraries from different SDKs.
- GUI applications should clearly choose their target backend, such as X11, Wayland, DRM/KMS, or Qt EGLFS.
- Vulkan and OpenCL workloads should be stress-tested with the target application load before release.
- Browser WebGL scenarios should verify the Chromium version, GPU blacklist, window system, and video composition path together.
- After updating the kernel, Mesa, Mali DDK, or root filesystem, re-run OpenGL, OpenGL ES, Vulkan, and OpenCL verification.
