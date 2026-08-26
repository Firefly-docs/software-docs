# 查看媒体信息

## FFmpeg 通用使用

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
