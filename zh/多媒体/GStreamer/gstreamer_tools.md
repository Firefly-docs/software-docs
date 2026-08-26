# GStreamer 常用工具

## 3. GStreamer 常用工具

### 3.1 gst-launch-1.0

用于在命令行中构建 Pipeline：

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

增加 `-v` 可打印 Caps 协商信息：

```bash
gst-launch-1.0 -v videotestsrc ! autovideosink
```

### 3.2 gst-play-1.0

用于快速播放本地文件或 URI：

```bash
gst-play-1.0 test.mp4
gst-play-1.0 https://example.com/test.mp4
```

常用选项：

| 选项 | 说明 |
| --- | --- |
| `--videosink=<element>` | 指定视频输出插件 |
| `--audiosink=<element>` | 指定音频输出插件 |
| `--use-playbin3` | 使用 Playbin3 |
| `--flags=<value>` | 控制视频、音频和字幕等播放功能 |

`--flags` 的常用值：

| 值 | 功能 |
| --- | --- |
| `1` | 仅视频 |
| `2` | 仅音频 |
| `3` | 视频和音频 |
| `4` | 字幕 |
| `7` | 视频、音频和字幕 |

### 3.3 gst-inspect-1.0

用于查询插件、Element、Pad、Caps、属性和默认值：

```bash
# 列出全部插件和 Element
gst-inspect-1.0

# 查看指定 Element
gst-inspect-1.0 waylandsink
gst-inspect-1.0 v4l2src
```

由于插件能力会随 GStreamer、SDK 和平台版本变化，文档中的属性只应作为参考，实际使用前应以本机输出为准。

### 3.4 gst-discoverer-1.0

用于分析文件或 URI 中的容器、轨道和编解码格式：

```bash
gst-discoverer-1.0 test.mp4
gst-discoverer-1.0 rtsp://127.0.0.1:8554/live
```

如果系统缺少 Demuxer、Parser 或 Decoder，该命令通常会给出相应提示。
