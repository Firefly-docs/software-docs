# OpenGL and Vulkan

OpenGL and Vulkan are used for desktop graphics, 3D rendering, visualization interfaces, games, and graphics benchmarks. OpenGL is a traditional graphics API, while Vulkan provides a modern low-overhead API with more explicit control.

## Install the Panfrost / Panthor Driver

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

## OpenGL Verification

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

## Vulkan Verification

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
