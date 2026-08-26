# 方案选择建议

- 需要完整桌面体验、浏览器、传统窗口应用或兼容旧 GUI 程序时，优先使用 X11。
- 需要嵌入式全屏 UI、多媒体显示、低延迟显示或轻量级图形系统时，优先使用 Wayland/Weston。
- Ubuntu/Debian 固件默认以 X11 为主。如果要验证 Wayland，应确认当前 GPU 驱动、Mesa、EGL、GBM 和应用后端是否匹配。
- Buildroot 固件默认以 Wayland/Weston 为主，应用开发时建议优先选择 Wayland、DRM/KMS 或 Qt EGLFS 等嵌入式显示后端。
