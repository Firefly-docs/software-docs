# Pad

## 2. 基本概念

### 2.3 Pad

Element 通过 Pad 连接：

- Source Pad 输出数据。
- Sink Pad 接收数据。
- Static Pad 在 Element 创建时就存在。
- Dynamic Pad 在识别到媒体轨道后动态创建，常见于 Demuxer 和 `decodebin`。

当容器包含视频和音频时，可为 Demuxer 命名并分别连接动态 Pad：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! qtdemux name=demux \
  demux.video_0 ! queue ! filesink location=video.bin \
  demux.audio_0 ! queue ! filesink location=audio.bin
```

Pad 名称可能随容器和插件版本变化，应通过 `gst-inspect-1.0 <element>` 查询。也可以使用 `demux.` 让 GStreamer 根据媒体类型进行协商。
