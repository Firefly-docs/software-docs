# Processing: Editing and Audio/Video Operations

## General FFmpeg Usage

### 5. Common Processing Operations

#### 5.4 Extracting a Segment

Start at one minute and output 30 seconds:

```bash
ffmpeg \
  -ss 00:01:00 \
  -i input.mp4 \
  -t 30 \
  -c copy \
  clip.mp4
```

With `-c copy`, copying can only begin at a nearby keyframe, so the start point may not be exact. Re-encode when frame-accurate trimming is required:

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

#### 5.5 Software Scaling, Cropping, and Rotation

Scale proportionally to 720p:

```bash
ffmpeg -i input.mp4 -vf 'scale=-2:720' -c:v libx264 -crf 23 -c:a copy output-720p.mp4
```

Crop the center to `1280x720`:

```bash
ffmpeg \
  -i input.mp4 \
  -vf "crop=1280:720:(iw-ow)/2:(ih-oh)/2" \
  -c:v libx264 \
  -crf 23 \
  -c:a copy \
  output-crop.mp4
```

Rotate clockwise by 90 degrees:

```bash
ffmpeg -i input.mp4 -vf 'transpose=clock' -c:v libx264 -crf 23 -c:a copy output-rotate.mp4
```

#### 5.6 Screenshots

Extract one frame at 10 seconds:

```bash
ffmpeg -ss 10 -i input.mp4 -frames:v 1 screenshot.png
```

Output one image every five seconds:

```bash
ffmpeg -i input.mp4 -vf 'fps=1/5' 'frame-%05d.jpg'
```

#### 5.7 Audio Processing

Extract an AAC audio stream without re-encoding:

```bash
ffmpeg -i input.mp4 -vn -c:a copy output.m4a
```

Convert to 48 kHz stereo AAC:

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

Adjust the volume:

```bash
ffmpeg -i input.wav -af 'volume=1.5' output.wav
```
