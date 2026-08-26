# 常用输出参数

## FFmpeg 通用使用

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
