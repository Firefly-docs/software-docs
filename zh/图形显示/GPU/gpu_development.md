# GPU 开发建议

- 产品固件优先使用经过验证的驱动组合，避免随意混用不同 SDK 的 GPU 库。
- 图形界面应用建议明确目标后端，例如 X11、Wayland、DRM/KMS 或 Qt EGLFS。
- Vulkan、OpenCL 等能力上线前，应使用目标负载进行长时间压力测试。
- 浏览器 WebGL 场景需要同时验证 Chromium 版本、GPU blacklist、窗口系统和视频合成路径。
- 更新内核、Mesa、Mali DDK 或根文件系统后，应重新执行 OpenGL、OpenGL ES、Vulkan 和 OpenCL 验证。
