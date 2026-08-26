# OpenGL ES

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

## 浏览器 WebGL 验证

Chromium 浏览器可以通过 GPU 页面检查硬件加速状态：

```text
chrome://gpu
```

如果 WebGL、WebGL2 或 Rasterization 显示为硬件加速，说明浏览器图形加速链路基本可用。也可以打开 WebGL 测试页面验证实际渲染效果。
