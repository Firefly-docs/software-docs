# 处理操作：封装与码流

## FFmpeg 通用使用

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
