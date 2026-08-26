# 安装与功能检查

## FFmpeg 通用使用

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
