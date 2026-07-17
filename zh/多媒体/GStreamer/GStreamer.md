# Linux GStreamer 使用与平台支持

## 1. GStreamer 简介

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

### 2.2 Pipeline

命令行中使用 `!` 连接 Element：

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

该 Pipeline 使用 `videotestsrc` 生成测试画面，再由 `autovideosink` 自动选择显示插件。

生产程序通常通过 GStreamer API 创建和控制 Pipeline；`gst-launch-1.0` 主要用于验证方案、复现问题和性能测试。

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

### 2.5 Queue、Tee 和多线程

`tee` 将一路数据复制为多路，`queue` 为不同分支建立独立线程和缓存。

```bash
gst-launch-1.0 videotestsrc \
  ! tee name=t \
  t. ! queue ! autovideosink \
  t. ! queue ! fakesink
```

分支后通常都应增加 `queue`。否则任意一路阻塞时，可能导致整个 Pipeline 停止。

### 2.6 Bin 和自动选路插件

GStreamer 提供多个用于自动构建播放链路的 Bin：

| Element | 作用 |
| --- | --- |
| `decodebin` / `decodebin3` | 自动选择 Parser 和 Decoder，并为不同轨道创建输出 Pad |
| `uridecodebin` / `uridecodebin3` | 根据 URI 自动选择 Source，并完成解封装和解码 |
| `playbin` / `playbin3` | 构建完整播放链路，并自动选择音视频 Sink |

快速验证时优先使用自动选路插件；需要固定硬件 Decoder、控制延迟或排查问题时，再显式构建 Pipeline。

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

### 4.3 摄像头预览

先查询视频设备支持的格式和分辨率：

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
```

使用 GStreamer 预览：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

如果摄像头输出 MJPEG，需要增加 JPEG 解码：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'image/jpeg,width=1920,height=1080,framerate=30/1' \
  ! jpegparse \
  ! decodebin \
  ! autovideosink
```

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

### 4.5 保存原始数据

`filesrc` 从文件读取数据，`filesink` 将数据保存为文件：

```bash
gst-launch-1.0 filesrc location=input.bin \
  ! filesink location=output.bin
```

处理 YUV 裸流时，每个 Buffer 通常应对应一帧。以 640×320 NV12 为例，一帧大小为 `640 × 320 × 1.5 = 307200` 字节：

```bash
gst-launch-1.0 filesrc location=input_nv12.yuv blocksize=307200 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=30/1' \
  ! fakesink
```

### 4.6 应用程序接入

`appsrc` 和 `appsink` 用于应用程序与 Pipeline 交换数据：

- `appsrc`：应用向 Pipeline 输入数据，例如输入解密后的私有码流。
- `appsink`：应用从 Pipeline 获取数据，例如取得解码帧并交给算法处理。

典型应用结构：

```text
自定义输入 → appsrc → GStreamer Pipeline → appsink → 图像算法
                                            │
                                            ▼
                         appsrc → 后处理/编码/显示
```

命令行主要用于验证，正式使用通常需要通过 GStreamer API 处理 Buffer、时间戳、EOS 和状态切换。

## 5. 通用调试和性能分析

### 5.1 开启日志

GStreamer 日志等级如下：

| 等级 | 数值 |
| --- | --- |
| ERROR | `1` |
| WARNING | `2` |
| FIXME | `3` |
| INFO | `4` |
| DEBUG | `5` |
| LOG | `6` |
| TRACE | `7` |

设置全局日志等级：

```bash
GST_DEBUG=2 gst-play-1.0 test.mp4
```

为不同模块设置不同等级：

```bash
GST_DEBUG='2,decodebin:5,v4l2*:4' gst-play-1.0 test.mp4
```

### 5.2 查看帧率

`fpsdisplaysink` 可以统计视频帧率，并将画面转交给真正的 Video Sink：

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=autovideosink \
      signal-fps-measurements=true text-overlay=false
```

`sync=false` 可用于测试 Pipeline 的最大吞吐量，但会关闭时钟同步，不代表正常播放行为。

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=fakesink \
      signal-fps-measurements=true text-overlay=false sync=false
```

### 5.3 导出 Pipeline 图

```bash
export GST_DEBUG_DUMP_DOT_DIR=/tmp
gst-play-1.0 test.mp4
ls -l /tmp/*.dot
```

在安装了 Graphviz 的主机上转换为图片：

```bash
dot pipeline.dot -Tpng -o pipeline.png
```

一次运行可能生成多个 DOT 文件，分别对应 Pipeline 在不同状态下的结构。

### 5.4 通用排障流程

1. 用 `gst-inspect-1.0` 确认插件存在，并检查 Pad、Caps 和属性。
2. 用 `gst-discoverer-1.0` 或 `v4l2-ctl` 确认输入格式。
3. 为 `gst-launch-1.0` 增加 `-v`，查看协商后的 Caps。
4. 将复杂 Pipeline 简化为 `Source → Parser/Decoder → fakesink`。
5. 再逐步加入格式转换、编码、显示、音频和网络分支。
6. 在 `tee`、Demuxer 或动态 Pad 后增加 `queue`，排除分支阻塞。
7. 使用 `GST_DEBUG` 定位加载插件、状态切换和协商错误。
8. 使用 `fpsdisplaysink` 区分输入、编解码、处理和显示瓶颈。
9. 导出 DOT 图确认实际创建的 Element 和连接关系。

## 6. 硬件平台支持

GStreamer 上层接口和 Pipeline 模型是通用的，但硬件编解码、零拷贝、内存类型、显示控制和性能优化通常由平台专用插件实现。

平台支持章节建议统一包含以下内容：

1. 平台架构和插件来源。
2. 支持的软件系统与 GStreamer 版本。
3. 硬件 Decoder 和 Encoder。
4. 摄像头、显示和零拷贝路径。
5. 平台专用环境变量。
6. 编译和部署方法。
7. 平台限制和常见问题。

当前文档的平台状态：

| 平台 | 支持状态 | 主要插件/接口 |
| --- | --- | --- |
| Rockchip | 已整理 | MPP、RGA、DRM/KMS、`gstreamer-rockchip` |
| 其他平台 | 待补充 |  |

## 7. Rockchip 平台支持

### 7.1 Rockchip 多媒体架构

Rockchip Linux SDK 通过 `gstreamer-rockchip` 对接 MPP（Media Process Platform），再由 MPP 调用 VPU 驱动完成硬件编解码。

```text
GStreamer 应用
      │
      ▼
gstreamer-rockchip 插件
      │
      ▼
Rockchip MPP
      │
      ▼
VPU 驱动和硬件编解码器
```

解码后的图像可通过 Wayland、X11 或 DRM/KMS 显示。部分链路还可使用 DMA Buffer、AFBC 和硬件 Plane 降低内存复制与 DDR 带宽。

### 7.2 软件源码位置

源码位置：

```text
<SDK>/external/gstreamer-rockchip
<SDK>/buildroot/package/gstreamer1
```

检查 Rockchip 插件：

```bash
gst-inspect-1.0 rockchipmpp
gst-inspect-1.0 mppvideodec
gst-inspect-1.0 mpph264enc
gst-inspect-1.0 mpph265enc
gst-inspect-1.0 mppjpegenc
```

### 7.3 Rockchip 插件概览

| 插件 | 作用 |
| --- | --- |
| `mppvideodec` | 使用 MPP/VPU 解码 H.263、H.264、H.265、VP8、VP9、MPEG、AV1 等视频 |
| `mppjpegdec` | 使用 MPP/VPU 解码 JPEG |
| `mpph264enc` | 使用 MPP/VPU 编码 H.264 |
| `mpph265enc` | 使用 MPP/VPU 编码 H.265 |
| `mppvp8enc` | 使用 MPP/VPU 编码 VP8 |
| `mppjpegenc` | 使用 MPP/VPU 编码 JPEG |
| `kmssrc` | 从 DRM/KMS 对象获取画面，可用于录屏 |
| `rkximagesink` | 通过 X11 和 DRM 进行零拷贝显示，需要独占硬件 Plane |

插件列出的格式不等于所有 Rockchip 芯片都支持，应同时确认芯片 Datasheet。

### 7.4 MPP 硬件解码

自动选择 Decoder：

```bash
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

当 `mppvideodec` 的 Rank 足够高且支持输入格式时，自动选路通常会选择 MPP Decoder。排障时建议显式指定：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! waylandsink
```

JPEG 硬件解码：

```bash
gst-launch-1.0 filesrc location=input.jpg \
  ! jpegparse \
  ! mppjpegdec \
  ! filesink location=output.yuv
```

`mppvideodec` 常用属性：

| 属性 | 说明 |
| --- | --- |
| `arm-afbc` | 输出 AFBC 压缩数据，默认关闭 |
| `fast-mode` | 开启 MPP Fast Mode，默认开启；部分平台可提高并行解码效率 |
| `ignore-error` | 忽略部分 MPP 解码错误并继续输出，默认开启 |
| `format` | 指定输出像素格式，可能通过 RGA 转换 |
| `width`、`height` | 指定输出尺寸，可能通过 RGA 缩放 |
| `rotation` | 旋转 0/90/180/270 度，可能通过 RGA 完成 |
| `crop-rectangle` | 按 `<x,y,width,height>` 裁剪 |
| `dma-feature` | 启用 DMA Buffer 特性；以当前插件说明为准 |

### 7.5 MPP 硬件编码

H.264 编码并封装为 MP4：

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph264enc \
  ! h264parse \
  ! qtmux \
  ! filesink location=h264.mp4
```

H.265 编码并封装为 MP4：

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph265enc \
  ! h265parse \
  ! qtmux \
  ! filesink location=h265.mp4
```

JPEG 编码：

```bash
gst-launch-1.0 videotestsrc num-buffers=1 \
  ! 'video/x-raw,format=NV12,width=640,height=320' \
  ! mppjpegenc \
  ! filesink location=output.jpg
```

视频编码器常用属性：

| 属性 | 说明 |
| --- | --- |
| `rc-mode` | 码率控制模式：`vbr`、`cbr` 或 `fixqp` |
| `bps` | 目标码率；`fixqp` 模式下忽略 |
| `bps-min`、`bps-max` | 最低和最高码率 |
| `gop` | I 帧间隔；默认 `-1` 时通常按帧率设置 |
| `qp-init`、`qp-min`、`qp-max` | 量化参数，QP 越低通常画质越高、码率越大 |
| `profile`、`level` | H.264/H.265 的 Profile 和 Level |
| `width`、`height`、`rotation` | 可能通过 RGA 完成缩放或旋转 |
| `arm-afbc` | 输入数据为 AFBC 时启用 |

属性和取值应以本机 `gst-inspect-1.0 <encoder>` 为准。

### 7.6 摄像头采集、编码和预览

Rockchip 平台仍通过标准 `v4l2src` 采集摄像头。下面将一路 NV12 数据分为硬件编码和显示两路：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12' \
  ! tee name=t \
  t. ! queue ! mpph264enc ! h264parse ! filesink location=/data/out.h264 \
  t. ! queue ! waylandsink
```

HDMI-IN 的用法与摄像头相同。可先查找名称为 `hdmirx` 的节点：

```bash
grep '' /sys/class/video4linux/*/name
```

再将 `v4l2src device=` 指向对应 `/dev/videoX`。

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

### 7.8 屏幕采集和录屏

`kmssrc` 可从 DRM/KMS 对象获取画面：

```bash
gst-launch-1.0 kmssrc ! waylandsink
```

录制为 H.264 裸流：

```bash
gst-launch-1.0 kmssrc sync-fb=0 \
  ! mpph264enc \
  ! h264parse \
  ! filesink location=/tmp/screen.h264
```

可通过 `connector-id`、`crtc-id`、`plane-id` 或 `fb-id` 指定采集对象，相关 ID 可用 `modetest` 查询。

### 7.9 Rockchip 显示路径

| Sink | 显示环境 | 特点 |
| --- | --- | --- |
| `waylandsink` | Wayland | 由 Wayland/GPU 合成，支持显示区域和层级等属性 |
| `xvimagesink` | X11/XVideo | 通过 X11/XVideo 显示，由 GPU 合成 |
| `kmssink` | DRM/KMS | 直接使用硬件 Plane，需要可用且独占的 Plane |
| `rkximagesink` | X11 + DRM | Rockchip 零拷贝显示插件，需要独占硬件 Plane |

Wayland 指定显示区域：

```bash
gst-launch-1.0 videotestsrc \
  ! waylandsink render-rectangle='<0,0,800,480>'
```

KMS 指定硬件 Plane：

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --videosink='kmssink plane-id=117'
```

查询 DRM Plane：

```bash
cat /sys/kernel/debug/dri/0/state
modetest -p
```

不同 Plane 支持的格式不同。例如部分芯片的 Cluster Plane 支持 AFBC，而 Esmart Plane 不支持。使用 `kmssink` 或 `rkximagesink` 前，应确认目标 Plane 未被其他显示客户端占用。

### 7.10 AFBC 带宽优化

AFBC（Arm Frame Buffer Compression）可降低帧缓冲占用的 DDR 带宽。原指南中 `mppvideodec` 的 AFBC 支持范围包括 H.264、H.265、VP9，以及 NV12、NV12 10-bit、NV16 等输出格式；最终能力取决于芯片和完整显示链路。

为单条 Pipeline 开启 AFBC：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec arm-afbc=true \
  ! waylandsink
```

为自动播放链路设置默认值：

```bash
export GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

检查 Decoder 是否输出 AFBC：

```bash
GST_DEBUG='*mpp*:4' gst-play-1.0 \
  --flags=3 --videosink=waylandsink test.mp4
```

日志出现类似内容时表示已经启用：

```text
applying NV12(AFBC) 1920x1080
```

注意事项：

- Decoder、Sink 和 DRM Plane 必须共同支持 AFBC。
- `kmssink` 和 `rkximagesink` 通常需要选择支持 AFBC 的 Cluster Plane。
- 明显缩放、旋转或特定显示方向下，AFBC 不一定获得更好性能。
- 开启后出现闪烁时，应检查系统性能模式、Plane 能力和缩放链路。

### 7.11 RGA 图像处理

Rockchip SDK 可让 `videoconvert`、`videoscale`、`videoflip`，以及 MPP 插件的格式转换、缩放、裁剪和旋转使用 RGA。

常见开关：

```bash
export GST_VIDEO_CONVERT_USE_RGA=1
export GST_VIDEO_FLIP_USE_RGA=1
```

格式转换：

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12' \
  ! videoconvert \
  ! 'video/x-raw,format=BGRA' \
  ! waylandsink
```

旋转：

```bash
gst-launch-1.0 videotestsrc \
  ! videoflip video-direction=90r \
  ! waylandsink
```

> 原指南指出，部分平台和软件版本（包括部分 RK3588 版本）的 RGA 路径可能异常，不建议在未验证时默认依赖。启用前应确认 SDK 修复状态，并测试像素格式、Stride、分辨率、旋转和缩放组合。出现异常时，可取消 RGA 环境变量，或避免使用 MPP 插件的 `format`、`width`、`height` 和 `rotation` 等隐式 RGA 路径。

### 7.12 环境变量

Rockchip SDK 通常在 `/etc/profile.d/gst.sh` 中集中设置相关环境变量。

| 环境变量 | 作用 |
| --- | --- |
| `GST_MPP_DEC_DISABLE_NV12_10=1` | 禁止直接输出 NV12 10-bit，并尝试转换为 NV12 |
| `GST_MPP_DEC_DISABLE_NV16_10=1` | 禁止直接输出 NV16 10-bit，并尝试转换为 NV12 |
| `GST_MPP_VIDEODEC_DEFAULT_FORMAT=NV12` | 令 MPP Decoder 默认输出 NV12，可能触发 RGA |
| `GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1` | 默认启用 MPP Decoder 的 AFBC 输出 |
| `GST_V4L2_PREFERRED_FOURCC=...` | 设置 V4L2 首选像素格式顺序 |
| `GST_VIDEO_CONVERT_PREFERRED_FORMAT=...` | 设置 `videoconvert` 首选输出格式顺序 |
| `GST_V4L2SRC_MAX_RESOLUTION=3840x2160` | 限制 `v4l2src` 可协商的最大分辨率 |
| `GST_V4L2_MIN_BUFS=64` | 设置 V4L2 驱动最小缓冲数量 |
| `GST_VIDEO_CONVERT_USE_RGA=1` | 对格式转换和缩放启用 RGA 路径 |
| `GST_VIDEO_FLIP_USE_RGA=1` | 对旋转和翻转启用 RGA 路径 |

建议只设置业务需要的变量，并在问题记录中保存当时的环境：

```bash
env | grep '^GST_' | sort
```

### 7.13 性能调试

确认实际使用 MPP Decoder：

```bash
GST_DEBUG='2,*mpp*:4' gst-play-1.0 test.mp4
```

测量解码和显示帧率：

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-play-1.0 --flags=3 \
  --videosink='fpsdisplaysink video-sink=waylandsink signal-fps-measurements=true text-overlay=false sync=false' \
  test.mp4
```

导出 Decoder 输出：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! filesink location=/tmp/decoded.yuv
```

不开启 AFBC 时，还可启用 MPP 自带 Dump：

```bash
export mpp_debug=0x400
```

AFBC 数据不能直接使用普通 YUV 播放器查看，需要配套解压工具以及正确的宽、高、格式和模式参数。

### 7.14 常见问题

#### 7.14.1 4K30 正常，4K60 卡顿

1. 确认实际使用 `mppvideodec`，而不是软件 Decoder。
2. 分别测试 Decoder 到 `fakesink` 和 Decoder 到显示 Sink 的帧率。
3. 使用 `--flags=3` 关闭字幕。
4. 检查 CPU、GPU、VPU 频率、系统负载和 DDR 带宽。
5. 在完整链路支持时对比测试 AFBC。
6. 仅定位吞吐量时测试 `sync=false`；正常播放一般保留同步。

#### 7.14.2 CPU 占用较高

常见原因是未使用 MPP 硬件解码、发生软件格式转换，或者字幕、缩放等处理开销较高。应结合 `GST_DEBUG` 和 DOT 图确认实际创建的 Decoder，以及是否插入了 `videoconvert`。

#### 7.14.3 播放卡住或进度为 0

可先尝试 Playbin3：

```bash
gst-play-1.0 --flags=3 --use-playbin3 test.mp4
```

同时使用 `gst-discoverer-1.0` 检查容器、轨道和缺失插件。

#### 7.14.4 有画面但没有声音

先用 `aplay` 验证声卡，再显式指定 ALSA 设备：

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --audiosink='alsasink device=hw:0,0'
```

#### 7.14.5 字幕导致卡顿

字幕合成可能包含图像截取、RGB 转换、混合和回转等操作。性能测试时可用 `--flags=3` 关闭字幕；高分辨率产品可考虑由 Qt 等 UI 框架在独立图层绘制字幕。

#### 7.14.6 `v4l2src` 无法满帧采集

先绕过 GStreamer 验证 V4L2 驱动能力：

```bash
v4l2-ctl -d /dev/video0 \
  --set-fmt-video=width=1920,height=1080,pixelformat=NV12 \
  --stream-mmap=3 --stream-skip=1 --stream-poll
```

如果 `v4l2-ctl` 满帧而 GStreamer 不满帧，可增加缓冲：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 min-buffers=64 \
  ! 'video/x-raw,width=1920,height=1080,format=NV12' \
  ! waylandsink
```

还应确认下游没有阻塞，并在编码、显示等分支前增加 `queue`。

#### 7.14.7 `v4l2src` 格式协商失败

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
gst-inspect-1.0 v4l2src
```

- 输出 NV12、NV16、BGRA、BGRx 等裸流时，可连接支持相同 Caps 的显示或编码插件。
- 输出 JPEG/MJPEG 时，应增加 `jpegparse ! mppjpegdec`。
- 上下游格式不匹配时可增加 `videoconvert`，但应关注 CPU 或 RGA 开销。

#### 7.14.8 无法采集 4K 以上分辨率

检查 `/etc/profile.d/gst.sh` 中的限制：

```bash
export GST_V4L2SRC_MAX_RESOLUTION=3840x2160
```

如果硬件、驱动和带宽均支持，可按需求提高该值并重新加载环境。
