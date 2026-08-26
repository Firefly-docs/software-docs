# GPU 常见问题

## glmark2 启动失败

如果 `glmark2` 或 `glmark2-es2` 启动失败，先确认当前图形环境：

```bash
echo $DISPLAY
echo $WAYLAND_DISPLAY
```

在 X11 环境中，通常需要有效的 `DISPLAY`。在 Wayland 或 DRM/KMS 环境中，需要使用匹配的 EGL 后端或测试工具参数。

## 性能低或显示为软件渲染

如果 `glmark2-es2` 显示 `llvmpipe`，说明当前使用 CPU 软件渲染。常见原因包括：

- GPU 驱动没有加载。
- 用户态 GPU 库与内核驱动不匹配。
- 图形会话没有使用正确的 EGL/DRI 驱动。
- 远程桌面、虚拟显示或错误的环境变量导致渲染回退。

可以先回到本地 HDMI/DP 显示环境验证，排除远程桌面和虚拟显示的影响。
