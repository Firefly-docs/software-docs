# 输出测试画面

## 4. GStreamer 通用使用方法

本章只使用标准或自动选择插件，不依赖特定硬件平台。平台专用的硬件加速命令参见后续章节。

### 4.1 输出测试画面

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

指定原始视频格式：

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1280,height=720,framerate=30/1' \
  ! autovideosink
```
