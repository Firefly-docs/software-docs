# X11/Wayland Introduction

Linux graphics systems are usually built from a display protocol, display server or compositor, desktop environment, window manager, and GPU driver. On Rockchip platforms, the common graphics display stacks are X11/Xserver and Wayland/Weston.

## Default Support

| System | Default graphics stack | Description |
| --- | --- | --- |
| Ubuntu/Debian | X11 | Firefly RK platform Ubuntu/Debian desktop firmware uses X11 by default. A common stack is `LXDE/XFCE + Xserver + lightdm`. Wayland can run with a supported kernel, Mesa, and open-source Panfrost / Panthor GPU driver stack. For GPU driver details, see [GPU](../GPU/gpu_driver.md). |
| Buildroot | Wayland | Firefly RK platform Buildroot graphics systems use Wayland by default. A common stack is `Weston + Wayland`, suitable for embedded full-screen UIs, multimedia display, and lightweight graphics applications. |

The actual behavior may vary by SoC, kernel version, GPU driver stack, and firmware configuration. Use the release notes and runtime environment of the actual firmware as the reference.
