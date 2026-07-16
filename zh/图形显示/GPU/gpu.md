# GPU

GPU 主要负责图形渲染、窗口合成、3D 加速、通用计算和部分浏览器 WebGL 场景。在 Firefly Linux 系统中，常见 GPU 能力包括 OpenGL、Vulkan、OpenGL ES、OpenCL 和浏览器硬件加速。

本文介绍 GPU 驱动环境、OpenGL/Vulkan、OpenGL ES、OpenCL 的验证方法，以及常见问题处理。不同芯片、内核版本和系统镜像中可用能力可能不同，请以实际 SDK、固件和板卡规格为准。

## GPU 驱动环境

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

## OpenGL 和 Vulkan

OpenGL 和 Vulkan 常用于桌面图形、3D 渲染、可视化界面、游戏和图形性能测试。OpenGL 更偏向传统图形 API，Vulkan 更偏向低开销、显式控制的现代图形 API。

### 安装 Panfrost / Panthor 驱动

在支持的系统镜像中，可以使用 Firefly 提供的脚本安装 Panfrost / Panthor 相关驱动包：

```bash
sudo panfrost.sh
sudo reboot
```

重启后检查驱动加载情况：

```bash
dmesg | grep -Ei "panfrost|panthor"
```

如果能看到 GPU 驱动初始化信息，表示内核侧驱动已经被识别。用户态 OpenGL / Vulkan 能力还需要结合 `glxinfo`、`glmark2`、`vulkaninfo` 等工具继续验证。

### OpenGL 验证

安装测试工具：

```bash
sudo apt update
sudo apt install glmark2 mesa-utils
```

运行 OpenGL 基准测试：

```bash
glmark2
```

常用检查点：

- `glxinfo -B` 中的 `OpenGL renderer string` 是否为期望的 GPU 驱动。
- `glmark2` 是否可以正常打开窗口并完成测试。
- 测试过程中是否出现花屏、卡死、窗口无法创建或软件渲染回退。

### Vulkan 验证

安装 Vulkan 工具：

```bash
sudo apt install vulkan-tools
```

查看 Vulkan 信息：

```bash
vulkaninfo --summary
```

运行基础示例：

```bash
vkcube
```

如果 `vkcube` 可以正常显示旋转立方体，说明 Vulkan 基础渲染链路可用。若提示找不到 ICD、实例创建失败或设备不可用，需要检查 Vulkan 驱动包、ICD 配置文件和当前 GPU 驱动栈是否匹配。

## OpenGL ES

OpenGL ES 是面向嵌入式设备的图形 API，常用于 Qt EGLFS、Wayland/Weston、GStreamer 显示、摄像头预览、工业 UI、浏览器 WebGL 和轻量 3D 应用。

不同芯片的 OpenGL ES 支持能力与 GPU 型号、驱动版本相关。以实际固件和驱动输出为准，可以通过以下命令查看：

```bash
glmark2-es2
```

如果系统没有相关工具，可以安装：

```bash
sudo apt update
sudo apt install glmark2-es2
```

运行 OpenGL ES 测试：

```bash
glmark2-es2
```

测试时重点关注：

- 窗口或全屏显示是否正常。
- EGL 初始化是否成功。
- 渲染器是否使用 GPU，而不是软件渲染。
- 长时间运行是否稳定。

### 浏览器 WebGL 验证

Chromium 浏览器可以通过 GPU 页面检查硬件加速状态：

```text
chrome://gpu
```

如果 WebGL、WebGL2 或 Rasterization 显示为硬件加速，说明浏览器图形加速链路基本可用。也可以打开 WebGL 测试页面验证实际渲染效果。

## OpenCL

OpenCL 用于 GPU 通用计算，常见于图像处理、并行计算、预处理、后处理和部分 AI 辅助任务。OpenCL 能力取决于芯片 GPU、驱动和用户态运行库。

安装并查看 OpenCL 信息：

```bash
sudo apt update
sudo apt install clinfo
clinfo
```

重点检查：

- 是否能识别到 OpenCL Platform。
- 是否能识别到 GPU Device。
- `Device Type` 是否包含 `GPU`。
- OpenCL C 版本、最大工作组大小、全局内存大小是否符合应用需求。

如果 `clinfo` 找不到平台或设备，通常需要检查：

- OpenCL 用户态库是否安装。
- ICD 配置是否存在并指向正确驱动库。
- 当前固件是否包含对应 GPU 的 OpenCL 支持。
- 是否混用了不匹配的 Mali DDK、Mesa、内核驱动或根文件系统。

## 常见问题

### glmark2 启动失败

如果 `glmark2` 或 `glmark2-es2` 启动失败，先确认当前图形环境：

```bash
echo $DISPLAY
echo $WAYLAND_DISPLAY
```

在 X11 环境中，通常需要有效的 `DISPLAY`。在 Wayland 或 DRM/KMS 环境中，需要使用匹配的 EGL 后端或测试工具参数。

### 性能低或显示为软件渲染

如果 `glmark2-es2` 显示 `llvmpipe`，说明当前使用 CPU 软件渲染。常见原因包括：

- GPU 驱动没有加载。
- 用户态 GPU 库与内核驱动不匹配。
- 图形会话没有使用正确的 EGL/DRI 驱动。
- 远程桌面、虚拟显示或错误的环境变量导致渲染回退。

可以先回到本地 HDMI/DP 显示环境验证，排除远程桌面和虚拟显示的影响。

## 开发建议

- 产品固件优先使用经过验证的驱动组合，避免随意混用不同 SDK 的 GPU 库。
- 图形界面应用建议明确目标后端，例如 X11、Wayland、DRM/KMS 或 Qt EGLFS。
- Vulkan、OpenCL 等能力上线前，应使用目标负载进行长时间压力测试。
- 浏览器 WebGL 场景需要同时验证 Chromium 版本、GPU blacklist、窗口系统和视频合成路径。
- 更新内核、Mesa、Mali DDK 或根文件系统后，应重新执行 OpenGL、OpenGL ES、Vulkan 和 OpenCL 验证。
