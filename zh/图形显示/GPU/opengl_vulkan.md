# OpenGL 和 Vulkan

OpenGL 和 Vulkan 常用于桌面图形、3D 渲染、可视化界面、游戏和图形性能测试。OpenGL 更偏向传统图形 API，Vulkan 更偏向低开销、显式控制的现代图形 API。

## 安装 Panfrost / Panthor 驱动

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

## OpenGL 验证

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

## Vulkan 验证

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
