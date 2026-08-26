# Element

## 2. 基本概念

### 2.1 Element

Element 是 Pipeline 的基本功能单元，通常分为以下几类：

| 类别 | 常见 Element | 作用 |
| --- | --- | --- |
| Source | `filesrc`、`v4l2src`、`videotestsrc`、`rtspsrc` | 产生或读取媒体数据 |
| Demuxer | `qtdemux`、`matroskademux`、`tsdemux` | 从容器中拆分音频、视频和字幕 |
| Depayloader | `rtph264depay`、`rtph265depay` | 从 RTP 包中提取编码码流 |
| Parser | `h264parse`、`h265parse`、`jpegparse` | 解析和整理编码码流 |
| Decoder | `decodebin`、具体软/硬件 Decoder | 将编码数据解码为原始音视频 |
| Encoder | 具体软/硬件 Encoder | 将原始音视频编码为压缩码流 |
| Filter | `videoconvert`、`videoscale`、`videoflip` | 转换格式、缩放、旋转或处理数据 |
| Sink | `autovideosink`、`waylandsink`、`filesink`、`fakesink` | 显示、保存或丢弃数据 |

可以使用下面的命令查看当前系统提供的 Element：

```bash
gst-inspect-1.0
gst-inspect-1.0 v4l2src
gst-inspect-1.0 h264parse
```
