# Rockchip 多媒体架构

## 7. Rockchip 平台支持

### 7.1 Rockchip 多媒体架构

Rockchip Linux SDK 通过 `gstreamer-rockchip` 对接 MPP（Media Process Platform），再由 MPP 调用 VPU 驱动完成硬件编解码。

```text
GStreamer 应用
      │
      ▼
gstreamer-rockchip 插件
      │
      ▼
Rockchip MPP
      │
      ▼
VPU 驱动和硬件编解码器
```

解码后的图像可通过 Wayland、X11 或 DRM/KMS 显示。部分链路还可使用 DMA Buffer、AFBC 和硬件 Plane 降低内存复制与 DDR 带宽。
