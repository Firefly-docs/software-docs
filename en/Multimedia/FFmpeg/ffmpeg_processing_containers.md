# Processing: Containers and Streams

## General FFmpeg Usage

### 5. Common Processing Operations

#### 5.1 Remuxing

Copy the compressed streams from an MP4 container to an MKV container:

```bash
ffmpeg -i input.mp4 -map 0 -c copy output.mkv
```

`-c copy` does not decode or re-encode streams, so performance is normally limited by storage I/O. The target container must support the original video, audio, and subtitle formats.

#### 5.2 Software Transcoding

Convert to H.264 video and AAC audio:

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

- `preset` controls the tradeoff between encoding speed and compression efficiency; it does not directly represent image quality.
- `crf` controls constant quality. With x264, lower values produce higher quality and larger files.
- `libx264` and `libx265` require FFmpeg to be built with the corresponding external libraries.

#### 5.3 Stream Selection

Select the first video stream and the second audio stream:

```bash
ffmpeg \
  -i input.mkv \
  -map 0:v:0 \
  -map 0:a:1 \
  -c copy \
  output.mkv
```

Continue processing even if the requested audio stream is missing:

```bash
ffmpeg -i input.mp4 -map 0:v:0 -map 0:a:0? -c copy output.mp4
```

The `?` marks the mapping as optional.
