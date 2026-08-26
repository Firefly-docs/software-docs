# Inspecting Media Information

## General FFmpeg Usage

### 4. Inspecting Media Information

#### 4.1 Quick Inspection

```bash
ffprobe -hide_banner input.mp4
```

Alternatively:

```bash
ffmpeg -hide_banner -i input.mp4
```

When no output file is provided, `ffmpeg -i` exits with an error but still prints input media information. Automated applications should use `ffprobe`.

#### 4.2 JSON Output

```bash
ffprobe \
  -v error \
  -show_format \
  -show_streams \
  -of json \
  input.mp4
```

Display only the codec, dimensions, and frame rate of the first video stream:

```bash
ffprobe \
  -v error \
  -select_streams v:0 \
  -show_entries stream=codec_name,width,height,pix_fmt,r_frame_rate \
  -of default=noprint_wrappers=1 \
  input.mp4
```

Display the media duration:

```bash
ffprobe \
  -v error \
  -show_entries format=duration \
  -of default=noprint_wrappers=1:nokey=1 \
  input.mp4
```
