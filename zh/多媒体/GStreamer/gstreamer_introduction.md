# GStreamer 简介

## Linux GStreamer 使用与平台支持

### 1. GStreamer 简介

GStreamer 是一个基于插件的开源多媒体框架，可用于音视频播放、采集、编解码、格式转换、网络传输、录制和媒体分析。

应用通过 Pipeline 将多个 Element 连接起来。数据从 Source 流入，经过解封装、解析、编解码和处理，最终到达 Sink。

```text
文件、摄像头或网络流
        │
        ▼
     Source
        │
        ▼
 Demuxer / Depayloader
        │
        ▼
      Parser
        │
        ▼
 Encoder / Decoder
        │
        ▼
 Convert / Scale / Filter
        │
        ▼
   Display / File / Network
```

GStreamer 的主要特点包括：

- 通过插件组合功能，应用无需直接处理每一种容器和编解码格式。
- 使用 Caps 进行媒体格式协商。
- 支持动态 Pad、多路音视频和分支 Pipeline。
- 同一套上层 Pipeline 可以对接软件编解码器或不同平台的硬件加速插件。
- 提供命令行工具、C API，以及多种语言绑定。
