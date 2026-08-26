# X11-Wayland

X11 和 Wayland 文档按默认方案、显示系统、Weston 配置和方案选择建议组织。

# 概览

- [X11/Wayland 介绍](overview.md) - 介绍 Ubuntu/Debian 和 Buildroot 的默认图形方案。

# X11/Xserver

- [X11/Xserver](x11.md) - 介绍传统桌面显示、Rockchip 增强功能和启动黑屏处理。

# Wayland

- [Wayland](wayland.md) - 介绍 Wayland 架构和会话检查方法。

# Weston

- [Weston](weston_overview.md) - 介绍 Weston 的作用、嵌入式应用场景和内置客户端工具。
- [Buildroot Weston 配置入口](weston_configuration.md) - 介绍 SDK 配置入口和显示 head 名称。
- [常用显示配置](weston_display.md) - 配置颜色格式、旋转、分辨率、缩放和运行时显示参数。
- [冻结屏幕和无黑屏](weston_startup.md) - 介绍 Weston 启动预热和冻结显示方法。
- [屏幕状态和多屏管理](weston_multidisplay.md) - 配置多屏策略、热拔插和 connector 状态。
- [输入设备和触屏校准](weston_input.md) - 配置输入要求、设备绑定和触屏校准。
- [性能相关配置](weston_performance.md) - 介绍 RGA、AFBC 和显示性能相关设置。

# 方案选择建议

- [方案选择建议](selection.md) - 根据产品场景选择 X11 或 Wayland/Weston。
