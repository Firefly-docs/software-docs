# X11/Wayland 介绍

Linux 图形系统通常由显示协议、显示服务器或合成器、桌面环境、窗口管理器和 GPU 驱动共同组成。Rockchip 平台常见的图形显示方案主要包括 X11/Xserver 和 Wayland/Weston 两类。

## 默认支持情况

| 系统 | 默认图形方案 | 说明 |
| --- | --- | --- |
| Ubuntu/Debian | X11 | Firefly RK 平台 Ubuntu/Debian 桌面固件默认使用 X11，常见组合为 `LXDE/XFCE + Xserver + lightdm`。如需运行 Wayland，可在支持的内核、Mesa 和开源 GPU 驱动环境下使用 Panfrost / Panthor，相关 GPU 驱动说明可参考 [GPU](../GPU/gpu_driver.md)。 |
| Buildroot | Wayland | Firefly RK 平台 Buildroot 图形系统默认使用 Wayland，常见组合为 `Weston + Wayland`，适合嵌入式全屏 UI、多媒体显示和轻量级图形应用。 |

不同芯片、内核版本、GPU 驱动栈和固件配置可能存在差异，请以实际固件发布说明和系统环境为准。
