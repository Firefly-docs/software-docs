# 播放 RTSP 流

## 4. GStreamer 通用使用方法

### 4.4 播放 RTSP 流

让 GStreamer 自动选择解码器：

```bash
gst-launch-1.0 uridecodebin uri=rtsp://192.168.1.100:8554/live \
  ! autovideosink
```

显式构建 H.264 接收链路：

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! decodebin \
  ! autovideosink
```

实际项目应根据网络状况调整 `rtspsrc` 的 `latency` 和传输协议。
