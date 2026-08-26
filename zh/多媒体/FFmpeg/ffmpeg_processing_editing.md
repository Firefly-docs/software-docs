# 处理操作：剪辑与音视频处理

## FFmpeg 通用使用

### 5. 常用处理操作

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
