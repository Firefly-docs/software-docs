# Processing: Concatenation and Network Streaming

## General FFmpeg Usage

### 5. Common Processing Operations

#### 5.8 Concatenating Multiple Files

When the containers, codecs, time bases, and stream parameters of several files match, use the concat demuxer. Create `list.txt`:

```text
file 'part-01.mp4'
file 'part-02.mp4'
file 'part-03.mp4'
```

Concatenate the files:

```bash
ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

If the file parameters differ, transcode them to matching formats first, or re-encode them with the concat filter.

#### 5.9 Network Streaming

Stream a file to an RTMP server at its native playback speed:

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

Publish to an RTSP server:

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

`-re` reads a file at its native timing, which is useful for simulating a live source. Do not use it for cameras, sound devices, or other inputs that are already real-time.
