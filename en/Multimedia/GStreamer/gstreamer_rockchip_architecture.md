# Rockchip Multimedia Architecture

## 7. Rockchip Platform Support

### 7.1 Rockchip Multimedia Architecture

The Rockchip Linux SDK connects GStreamer to MPP (Media Process Platform) through `gstreamer-rockchip`. MPP then invokes the VPU driver to perform hardware encoding and decoding.

```text
GStreamer application
      │
      ▼
gstreamer-rockchip plugin
      │
      ▼
Rockchip MPP
      │
      ▼
VPU driver and hardware codecs
```

Decoded images can be displayed through Wayland, X11, or DRM/KMS. Some paths can also use DMA buffers, AFBC, and hardware planes to reduce memory copies and DDR bandwidth usage.
