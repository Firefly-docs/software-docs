# 播放本地文件

## 4. GStreamer 通用使用方法

### 4.2 播放本地文件

最简单的播放方法：

```bash
gst-play-1.0 test.mp4
```

使用 `playbin`：

```bash
gst-launch-1.0 playbin uri=file:///absolute/path/test.mp4
```

使用 `uridecodebin` 并指定视频输出：

```bash
gst-launch-1.0 uridecodebin uri=file:///absolute/path/test.mp4 \
  ! autovideosink
```

文件 URI 应使用绝对路径，并写成 `file:///path/to/file`。
