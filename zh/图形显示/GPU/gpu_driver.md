# GPU 驱动环境

GPU 主要负责图形渲染、窗口合成、3D 加速、通用计算和部分浏览器 WebGL 场景。在 Firefly Linux 系统中，常见 GPU 能力包括 OpenGL、Vulkan、OpenGL ES、OpenCL 和浏览器硬件加速。

本文介绍 GPU 驱动环境、OpenGL/Vulkan、OpenGL ES、OpenCL 的验证方法，以及常见问题处理。不同芯片、内核版本和系统镜像中可用能力可能不同，请以实际 SDK、固件和板卡规格为准。

Rockchip 平台常见 GPU 驱动栈包括：

- 官方 Mali DDK 用户态驱动：通常随 Firefly 官方固件预装，适合稳定产品环境。
- Mesa Panfrost / Panthor 开源驱动：适合在支持的内核和 Mesa 版本上验证开源 OpenGL、Vulkan 能力。

在 Kernel 6.1 SDK 中，RK3588 和 RK3576 可使用 Mesa 25.0.7 的 Panthor / Panfrost 驱动栈。切换驱动栈前建议先确认当前系统镜像、内核版本、Mesa 版本和产品需求。

查看系统 GPU 相关信息：

```bash
uname -a
lsmod | grep -E "mali|panfrost|panthor|drm"
dmesg | grep -Ei "mali|panfrost|panthor|gpu"
```
