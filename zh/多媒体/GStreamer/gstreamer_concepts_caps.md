# Caps 和格式协商

## 2. 基本概念

### 2.4 Caps 和格式协商

Caps 描述 Pad 支持的媒体类型和参数，例如像素格式、分辨率和帧率：

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

常见 Caps 类型：

| Caps | 含义 |
| --- | --- |
| `video/x-raw` | 未压缩视频帧 |
| `video/x-h264` | H.264 编码码流 |
| `video/x-h265` | H.265 编码码流 |
| `image/jpeg` | JPEG/MJPEG 数据 |
| `audio/x-raw` | 未压缩音频数据 |

如果上下游 Caps 不兼容，Pipeline 会出现 `not-negotiated` 等协商错误。此时需要检查实际格式，并按需插入 Parser、Decoder、`videoconvert` 或 `videoscale`。
