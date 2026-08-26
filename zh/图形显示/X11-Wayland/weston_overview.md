# Weston

Weston 是 Wayland 协议的参考合成器，也是嵌入式 Linux 中常用的 Wayland 显示服务器实现。它可以直接基于 DRM/KMS 输出显示，也可以作为桌面 shell、全屏 shell 或 kiosk 类图形环境使用。

在 Buildroot 或 Yocto 构建的嵌入式桌面系统中，常见组合为：

```text
Weston + Wayland
```

Rockchip Buildroot SDK 默认使用 Weston DRM 后端作为显示服务。Weston 应用可提供状态栏、背景、Chromium 浏览器、Terminal 终端、Launcher、摄像头预览、多路视频、GPU 和鼠标等基础演示功能。系统中也通常包含一些 Weston 客户端测试程序，例如：

```bash
weston-simple-egl
weston-simple-shm
weston-simple-touch
weston-screenshooter
weston-terminal
weston-touch-calibrator
```

Weston 适合以下场景：

- 嵌入式全屏 UI。
- 多媒体播放和视频显示。
- Qt、GTK、GStreamer 等支持 Wayland 后端的应用。
- 不需要完整传统桌面环境的产品系统。

查看 Weston 服务状态：

```bash
systemctl status weston
```

查看 Weston 版本：

```bash
weston --version
```
