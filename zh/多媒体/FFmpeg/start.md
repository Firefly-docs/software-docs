# FFmpeg 使用指南

## FFmpeg 通用使用

### 1. FFmpeg 简介

FFmpeg 是一套开源的多媒体处理工具和库，可用于视频、音频、字幕和元数据的解析、转换、编解码、滤镜处理、封装和网络传输。

FFmpeg 的常用命令行工具包括：

| 工具 | 作用 |
| --- | --- |
| `ffmpeg` | 转码、转封装、采集、滤镜处理和推流 |
| `ffprobe` | 查看媒体容器、码流、帧和元数据信息 |
| `ffplay` | 用于测试和调试的简易播放器 |

主要库如下：

| 库 | 作用 |
| --- | --- |
| `libavcodec` | 视频和音频编解码 |
| `libavformat` | 媒体封装格式、解封装和网络协议 |
| `libavfilter` | 音视频滤镜图 |
| `libavutil` | 像素格式、时间基、数据结构和通用工具 |
| `libswscale` | 软件图像缩放和像素格式转换 |
| `libswresample` | 音频重采样、声道转换和混音 |
| `libavdevice` | 摄像头、声卡和显示设备输入输出 |

FFmpeg 处理链路可以简化为：

```text
输入文件 / 摄像头 / 网络流
                │
                ▼
             解封装
                │
                ▼
              解码
                │
                ▼
        视频或音频滤镜
                │
                ▼
              编码
                │
                ▼
              封装
                │
                ▼
       输出文件 / 网络流
```

转封装时可以跳过解码、滤镜和编码，直接复制压缩码流。这种方式速度快且不会产生画质损失。

### 2. 安装与功能检查

Debian/Ubuntu 可以安装发行版提供的标准 FFmpeg：

```bash
sudo apt update
sudo apt install ffmpeg
```

查看版本和编译选项：

```bash
ffmpeg -version
ffmpeg -buildconf
```

查看当前版本支持的能力：

```bash
ffmpeg -formats
ffmpeg -codecs
ffmpeg -encoders
ffmpeg -decoders
ffmpeg -filters
ffmpeg -protocols
ffmpeg -hwaccels
```

不同发行版的编译选项不同。例如，使用 `libx264`、`libx265`、SRT 或某些网络协议前，必须先确认当前 FFmpeg 已启用对应编码器或外部库。

> 发行版自带的标准 FFmpeg 通常不包含下文介绍的 `rkmpp` 编解码器和 `rkrga` 滤镜。Rockchip 硬件加速需要使用对应的 `ffmpeg-rockchip` 版本。

### 3. 命令基本结构

FFmpeg 命令的基本结构为：

```bash
ffmpeg [全局选项] [输入选项] -i <输入> [输出选项] <输出>
```

例如：

```bash
ffmpeg -hide_banner -i input.mp4 -c:v libx264 -c:a aac output.mp4
```

选项的位置很重要：

- 放在 `-i` 前的选项通常属于后面的输入。
- 放在输出文件前的选项通常属于该输出。
- 多个输入和输出可以分别使用不同选项。

常用全局选项：

| 选项 | 说明 |
| --- | --- |
| `-hide_banner` | 隐藏版本 Banner，精简日志 |
| `-y` | 直接覆盖已存在的输出文件 |
| `-n` | 输出文件已存在时终止 |
| `-loglevel <level>` | 设置日志级别，例如 `error`、`info`、`verbose`、`debug` |
| `-stats` | 显示实时处理进度 |
| `-benchmark` | 输出处理时间和资源统计 |

### 4. 查看媒体信息

#### 4.1 快速查看

```bash
ffprobe -hide_banner input.mp4
```

或者：

```bash
ffmpeg -hide_banner -i input.mp4
```

`ffmpeg -i` 在没有输出文件时会报错退出，但仍会打印输入媒体信息。自动化程序建议使用 `ffprobe`。

#### 4.2 输出 JSON

```bash
ffprobe \
  -v error \
  -show_format \
  -show_streams \
  -of json \
  input.mp4
```

只获取第一路视频的编码、宽高和帧率：

```bash
ffprobe \
  -v error \
  -select_streams v:0 \
  -show_entries stream=codec_name,width,height,pix_fmt,r_frame_rate \
  -of default=noprint_wrappers=1 \
  input.mp4
```

获取时长：

```bash
ffprobe \
  -v error \
  -show_entries format=duration \
  -of default=noprint_wrappers=1:nokey=1 \
  input.mp4
```

### 5. 常用处理操作

#### 5.1 转封装

将 MP4 中的压缩码流直接复制到 MKV：

```bash
ffmpeg -i input.mp4 -map 0 -c copy output.mkv
```

`-c copy` 不解码和重新编码，速度通常受存储 I/O 限制。但目标封装必须支持原始视频、音频和字幕格式。

#### 5.2 软件转码

转换为 H.264 + AAC：

```bash
ffmpeg \
  -i input.mkv \
  -c:v libx264 \
  -preset medium \
  -crf 23 \
  -c:a aac \
  -b:a 192k \
  output.mp4
```

- `preset` 控制编码速度与压缩效率的取舍，不直接表示画质。
- `crf` 控制恒定质量；对 x264 而言数值越小画质越高、文件越大。
- `libx264` 和 `libx265` 需要 FFmpeg 编译时启用对应外部库。

#### 5.3 选择码流

选择第一路视频和第二路音频：

```bash
ffmpeg \
  -i input.mkv \
  -map 0:v:0 \
  -map 0:a:1 \
  -c copy \
  output.mkv
```

音频不存在时也继续处理：

```bash
ffmpeg -i input.mp4 -map 0:v:0 -map 0:a:0? -c copy output.mp4
```

`?` 表示该映射是可选的。

#### 5.4 截取片段

从 1 分钟处开始，输出 30 秒：

```bash
ffmpeg \
  -ss 00:01:00 \
  -i input.mp4 \
  -t 30 \
  -c copy \
  clip.mp4
```

`-c copy` 只能从附近关键帧开始拷贝，截取点可能不精确。需要帧精确截取时应重新编码：

```bash
ffmpeg \
  -ss 00:01:00 \
  -i input.mp4 \
  -t 30 \
  -c:v libx264 \
  -crf 23 \
  -c:a aac \
  clip-accurate.mp4
```

#### 5.5 软件缩放、裁剪和旋转

按比例缩放到 720p：

```bash
ffmpeg -i input.mp4 -vf 'scale=-2:720' -c:v libx264 -crf 23 -c:a copy output-720p.mp4
```

中心裁剪为 `1280x720`：

```bash
ffmpeg \
  -i input.mp4 \
  -vf "crop=1280:720:(iw-ow)/2:(ih-oh)/2" \
  -c:v libx264 \
  -crf 23 \
  -c:a copy \
  output-crop.mp4
```

顺时针旋转 90°：

```bash
ffmpeg -i input.mp4 -vf 'transpose=clock' -c:v libx264 -crf 23 -c:a copy output-rotate.mp4
```

#### 5.6 截图

提取第 10 秒的一帧：

```bash
ffmpeg -ss 10 -i input.mp4 -frames:v 1 screenshot.png
```

每 5 秒输出一张图片：

```bash
ffmpeg -i input.mp4 -vf 'fps=1/5' 'frame-%05d.jpg'
```

#### 5.7 音频处理

直接提取 AAC 音频：

```bash
ffmpeg -i input.mp4 -vn -c:a copy output.m4a
```

转换为 48 kHz 双声道 AAC：

```bash
ffmpeg \
  -i input.mp4 \
  -vn \
  -ar 48000 \
  -ac 2 \
  -c:a aac \
  -b:a 192k \
  output.m4a
```

调整音量：

```bash
ffmpeg -i input.wav -af 'volume=1.5' output.wav
```

#### 5.8 合并多个文件

当多个文件的封装、编码、时间基和码流参数一致时，可使用 concat demuxer。创建 `list.txt`：

```text
file 'part-01.mp4'
file 'part-02.mp4'
file 'part-03.mp4'
```

执行合并：

```bash
ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

如果各文件参数不一致，需要先转码为相同格式，或使用 concat filter 重新编码。

#### 5.9 网络推流

将文件按实时速度推送到 RTMP 服务器：

```bash
ffmpeg \
  -re \
  -i input.mp4 \
  -c:v libx264 \
  -preset veryfast \
  -b:v 4M \
  -maxrate 4M \
  -bufsize 8M \
  -g 60 \
  -c:a aac \
  -b:a 128k \
  -f flv \
  rtmp://server.example.com/live/stream
```

发布到 RTSP 服务器：

```bash
ffmpeg \
  -re \
  -i input.mp4 \
  -c:v libx264 \
  -preset veryfast \
  -c:a aac \
  -f rtsp \
  rtsp://server.example.com/live/stream
```

`-re` 使文件输入按原始时间速度读取，适合模拟实时源，不应用于摄像头、声卡等本身就是实时的输入。

### 6. 常用输出参数

| 选项 | 说明 |
| --- | --- |
| `-c:v` | 视频编码器，`copy` 表示直接复制 |
| `-c:a` | 音频编码器 |
| `-b:v` / `-b:a` | 视频或音频目标码率 |
| `-maxrate` | 视频最大码率约束 |
| `-bufsize` | 码率控制缓冲区 |
| `-r` | 输出帧率 |
| `-g` | GOP 长度，即关键帧间隔 |
| `-pix_fmt` | 输出像素格式 |
| `-ar` | 音频采样率 |
| `-ac` | 音频声道数 |
| `-movflags +faststart` | 将 MP4 索引移到文件头，便于网页渐进式播放 |

用于网页播放的 MP4 示例：

```bash
ffmpeg \
  -i input.mp4 \
  -c:v libx264 \
  -crf 23 \
  -c:a aac \
  -movflags +faststart \
  output-web.mp4
```

### 7. 通用问题排查

| 现象 | 检查方向 |
| --- | --- |
| `Unknown encoder` | 使用 `ffmpeg -encoders` 检查编码器是否已编译 |
| `Invalid data found when processing input` | 检查文件是否完整、封装格式是否正确 |
| `Could not find codec parameters` | 增大 `-analyzeduration` 和 `-probesize`，或检查直播流是否包含完整头信息 |
| `Non-monotonous DTS` | 输入时间戳异常，检查拼接、转封装和采集时钟 |
| 输出无音频或字幕 | 使用 `ffprobe` 和 `-map` 显式选择码流 |
| 文件过大 | 调整 CRF、码率、分辨率或帧率 |
| 处理速度较慢 | 检查编码 `preset`、软件滤镜、I/O 和硬件加速状态 |

获取详细日志：

```bash
ffmpeg -loglevel verbose -i input.mp4 <output-options> output.mp4
```

保存可用于问题报告的完整日志：

```bash
ffmpeg -report -i input.mp4 <output-options> output.mp4
```

## Rockchip 平台

### 1. 概述

`ffmpeg-rockchip` 是面向 Rockchip 平台扩展的 FFmpeg 分支，使用 MPP（Media Process Platform）完成硬件视频编解码，使用 RGA（2D Raster Graphic Acceleration）完成缩放、裁剪、旋转、像素格式转换和图层叠加。

典型硬件处理链路如下：

```text
输入 → MPP 硬件解码 → DRM_PRIME/DMA-BUF
                         │
                         ▼
                    RGA 图像处理
                         │
                         ▼
                    MPP 硬件编码 → 输出
```

视频帧保持为 `drm_prime` 硬件帧时，可以减少 CPU 内存和硬件视频缓冲区之间的数据复制，降低 CPU 占用和 DDR 带宽开销。

> `ffmpeg-rockchip` 是非官方 FFmpeg 分支。实际使用和开源许可应以项目源码中的 README 和 License 文件为准。

### 2. 支持的功能

MPP 硬件解码器：

| FFmpeg 名称 | 格式 |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `vp8_rkmpp` / `vp9_rkmpp` | VP8/VP9 |
| `av1_rkmpp` | AV1 |
| `mjpeg_rkmpp` | Motion JPEG |
| `mpeg1_rkmpp` / `mpeg2_rkmpp` / `mpeg4_rkmpp` | MPEG Video |
| `h263_rkmpp` | H.263 |

MPP 硬件编码器：

| FFmpeg 名称 | 格式 |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `mjpeg_rkmpp` | Motion JPEG |

RGA 硬件滤镜：

| 滤镜 | 作用 |
| --- | --- |
| `scale_rkrga` | 缩放和像素格式转换 |
| `vpp_rkrga` | 缩放、裁剪、旋转和翻转 |
| `overlay_rkrga` | 视频或图像叠加 |

源码中注册某个编解码器，不代表所有 Rockchip SoC 都具有对应硬件能力。最大分辨率、帧率、位深、Profile、Level 和并发路数应以目标 SoC 的 Datasheet、TRM 和实际 BSP 为准。

### 3. 运行要求

`ffmpeg-rockchip` 需要 Rockchip BSP/vendor Kernel。项目主要测试了 Kernel 5.10 和 6.1。

编译和运行需要：

- Rockchip MPP 1.3.9 或更高版本。
- 与 BSP 匹配的 librga。
- libdrm 和 DRM_PRIME/DMA-BUF 支持。
- MPP、RGA 和 DRM 设备节点的访问权限。

常见设备节点：

```text
/dev/mpp_service
/dev/rga
/dev/dri/
/dev/dma_heap/
```

检查设备节点：

```bash
ls -l /dev/mpp_service /dev/rga /dev/dri /dev/dma_heap 2>/dev/null
```

运行 FFmpeg 的用户通常需要属于设备节点对应的 `video` 或 `render` 用户组：

```bash
id
sudo usermod -aG video,render "$USER"
```

重新登录后用户组配置才会生效。量产系统建议使用 udev 规则配置权限，不要长期使用 `chmod 777`。

### 4. 编译与检查

检查依赖：

```bash
pkg-config --modversion libdrm
pkg-config --modversion rockchip_mpp
pkg-config --modversion librga
```

获取并编译源码：

```bash
git clone https://github.com/nyanmisaka/ffmpeg-rockchip.git
cd ffmpeg-rockchip

./configure \
  --prefix=/usr/local \
  --enable-version3 \
  --enable-libdrm \
  --enable-rkmpp \
  --enable-rkrga \
  --enable-shared \
  --disable-static

make -j$(nproc)
sudo make install
sudo ldconfig
```

确认当前调用的是正确的 FFmpeg：

```bash
command -v ffmpeg
ffmpeg -version
```

检查 Rockchip 硬件功能：

```bash
ffmpeg -hide_banner -hwaccels | grep -i rkmpp
ffmpeg -hide_banner -decoders | grep rkmpp
ffmpeg -hide_banner -encoders | grep rkmpp
ffmpeg -hide_banner -filters  | grep rkrga
```

如果没有对应条目，通常是当前运行了系统自带的标准 FFmpeg，或者编译时未成功启用 MPP/RGA。

### 5. 使用示例

#### 5.1 硬件解码测试

```bash
ffmpeg \
  -hwaccel rkmpp \
  -hwaccel_output_format drm_prime \
  -i input.mp4 \
  -an -f null -
```

也可以显式指定解码器：

```bash
ffmpeg -c:v h264_rkmpp -i input-h264.mp4 -an -f null -
ffmpeg -c:v hevc_rkmpp -i input-hevc.mp4 -an -f null -
```

#### 5.2 H.264 硬件编码

```bash
ffmpeg \
  -i input.mp4 \
  -c:v h264_rkmpp \
  -rc_mode VBR \
  -b:v 8M \
  -maxrate 10M \
  -g 60 \
  -c:a copy \
  output-h264.mp4
```

#### 5.3 HEVC 硬件编码

```bash
ffmpeg \
  -i input.mp4 \
  -c:v hevc_rkmpp \
  -rc_mode VBR \
  -b:v 6M \
  -maxrate 8M \
  -g 60 \
  -c:a copy \
  output-hevc.mp4
```

H.264 和 HEVC 编码器支持 `VBR`、`CBR`、`CQP` 和 `AVBR` 码率控制。可通过以下命令查看当前版本的完整参数：

```bash
ffmpeg -hide_banner -h encoder=h264_rkmpp
ffmpeg -hide_banner -h encoder=hevc_rkmpp
```

#### 5.4 零拷贝缩放转码

```bash
ffmpeg \
  -hwaccel rkmpp \
  -hwaccel_output_format drm_prime \
  -i input.mp4 \
  -vf 'scale_rkrga=w=1920:h=1080:format=nv12' \
  -c:v h264_rkmpp \
  -rc_mode VBR \
  -b:v 8M \
  -c:a copy \
  output-1080p.mp4
```

此命令使用 MPP 解码、RGA 缩放和 MPP 编码。视频帧通过 DRM_PRIME/DMA-BUF 在硬件模块之间传递。

使用其他 RGA 功能前可查看滤镜帮助：

```bash
ffmpeg -hide_banner -h filter=scale_rkrga
ffmpeg -hide_banner -h filter=vpp_rkrga
ffmpeg -hide_banner -h filter=overlay_rkrga
```

### 6. 常见问题

| 现象 | 检查方法 |
| --- | --- |
| `Unknown decoder/encoder` | 使用 `ffmpeg -version` 和 `ffmpeg -encoders/-decoders` 确认当前版本 |
| 无法打开 MPP/RGA 设备 | 检查设备节点、用户组、Kernel 驱动和 DTS |
| RGA 提示 `No hw context provided` | 解码时加入 `-hwaccel_output_format drm_prime` |
| RGA 提示格式或尺寸不支持 | 改用 `nv12` 等常用格式，并检查宽高和裁剪坐标对齐 |
| 硬件加速后 CPU 占用仍较高 | 检查是否使用了软件滤镜，导致 `hwdownload` 和 `hwupload` |
| 某个格式无法硬解 | 检查目标 SoC 是否支持该编码、位深、Profile 和分辨率 |

查看详细日志：

```bash
ffmpeg -loglevel verbose <other-options>
dmesg | grep -Ei 'mpp|rga|vpu|rkvdec|rkvenc'
```

参考资料：

- [ffmpeg-rockchip](https://github.com/nyanmisaka/ffmpeg-rockchip)
- [Rockchip MPP](https://github.com/rockchip-linux/mpp)
- [Rockchip RGA](https://github.com/airockchip/librga)
