# Wayland

Wayland 是新一代 Linux 显示协议，用于替代传统 X11 架构。Wayland 协议将显示服务器和合成器角色合并，应用通过 Wayland 协议直接与合成器通信，再由合成器完成窗口合成、输入分发和显示输出。

Wayland 架构更适合现代图形栈、嵌入式 UI、低延迟显示和更直接的 DRM/KMS 显示链路。它通常依赖 EGL、GBM、DRM/KMS 和 GPU 驱动配合工作。

查看当前会话是否为 Wayland：

```bash
echo $XDG_SESSION_TYPE
```

如果输出为 `wayland`，表示当前会话运行在 Wayland 环境中。

也可以查看 Wayland 显示环境变量：

```bash
echo $WAYLAND_DISPLAY
```
