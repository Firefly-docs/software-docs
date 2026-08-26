# Common Output Options

## General FFmpeg Usage

### 6. Common Output Options

| Option | Description |
| --- | --- |
| `-c:v` | Video encoder; `copy` copies the stream directly |
| `-c:a` | Audio encoder |
| `-b:v` / `-b:a` | Target video or audio bitrate |
| `-maxrate` | Maximum video bitrate constraint |
| `-bufsize` | Rate-control buffer size |
| `-r` | Output frame rate |
| `-g` | GOP length, or keyframe interval |
| `-pix_fmt` | Output pixel format |
| `-ar` | Audio sample rate |
| `-ac` | Number of audio channels |
| `-movflags +faststart` | Move the MP4 index to the beginning for progressive web playback |

Create an MP4 suitable for web playback:

```bash
ffmpeg \
  -i input.mp4 \
  -c:v libx264 \
  -crf 23 \
  -c:a aac \
  -movflags +faststart \
  output-web.mp4
```
