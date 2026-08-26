# RTSP 硬件解码

## 7. Rockchip 平台支持

### 7.7 RTSP 硬件解码

H.264 RTSP 流：

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! mppvideodec \
  ! waylandsink
```

H.265 流应使用 `rtph265depay ! h265parse ! mppvideodec`。
