# FFmpeg User Guide

## General FFmpeg Usage

### 1. Introduction to FFmpeg

FFmpeg is a collection of open-source multimedia processing tools and libraries. It can parse, convert, encode, decode, filter, mux, and transmit video, audio, subtitles, and metadata.

Common FFmpeg command-line tools include:

| Tool | Purpose |
| --- | --- |
| `ffmpeg` | Transcoding, remuxing, capture, filtering, and streaming |
| `ffprobe` | Inspecting media containers, streams, frames, and metadata |
| `ffplay` | A simple player for testing and debugging |

The main libraries are:

| Library | Purpose |
| --- | --- |
| `libavcodec` | Video and audio encoding and decoding |
| `libavformat` | Media containers, demuxing, muxing, and network protocols |
| `libavfilter` | Audio and video filter graphs |
| `libavutil` | Pixel formats, time bases, data structures, and common utilities |
| `libswscale` | Software image scaling and pixel-format conversion |
| `libswresample` | Audio resampling, channel conversion, and mixing |
| `libavdevice` | Input and output for cameras, sound devices, and displays |

An FFmpeg processing pipeline can be simplified as follows:

```text
Input file / camera / network stream
                │
                ▼
             Demuxing
                │
                ▼
              Decoding
                │
                ▼
         Video or audio filters
                │
                ▼
              Encoding
                │
                ▼
              Muxing
                │
                ▼
       Output file / network stream
```

Remuxing skips decoding, filtering, and encoding, and directly copies the compressed streams. It is fast and does not reduce media quality.

### 2. Installation and Feature Checks

On Debian or Ubuntu, install the standard FFmpeg package provided by the distribution:

```bash
sudo apt update
sudo apt install ffmpeg
```

Display the version and build configuration:

```bash
ffmpeg -version
ffmpeg -buildconf
```

Display the capabilities supported by the current build:

```bash
ffmpeg -formats
ffmpeg -codecs
ffmpeg -encoders
ffmpeg -decoders
ffmpeg -filters
ffmpeg -protocols
ffmpeg -hwaccels
```

Build options differ between distributions. Before using `libx264`, `libx265`, SRT, or a particular network protocol, confirm that the current FFmpeg build includes the corresponding encoder or external library.

> Standard FFmpeg packages supplied by distributions normally do not include the `rkmpp` codecs and `rkrga` filters described below. Rockchip hardware acceleration requires a suitable `ffmpeg-rockchip` build.

### 3. Basic Command Structure

The basic structure of an FFmpeg command is:

```bash
ffmpeg [global options] [input options] -i <input> [output options] <output>
```

For example:

```bash
ffmpeg -hide_banner -i input.mp4 -c:v libx264 -c:a aac output.mp4
```

Option placement is important:

- Options before `-i` normally apply to the following input.
- Options before an output file normally apply to that output.
- Different options can be assigned to each input and output.

Common global options:

| Option | Description |
| --- | --- |
| `-hide_banner` | Hide the version banner and reduce log output |
| `-y` | Overwrite existing output files without asking |
| `-n` | Stop if the output file already exists |
| `-loglevel <level>` | Set the log level, such as `error`, `info`, `verbose`, or `debug` |
| `-stats` | Display real-time processing progress |
| `-benchmark` | Print processing-time and resource statistics |

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

### 7. General Troubleshooting

| Symptom | What to check |
| --- | --- |
| `Unknown encoder` | Use `ffmpeg -encoders` to confirm that the encoder was built |
| `Invalid data found when processing input` | Check file integrity and whether the detected container format is correct |
| `Could not find codec parameters` | Increase `-analyzeduration` and `-probesize`, or check whether the live stream contains complete headers |
| `Non-monotonous DTS` | Inspect input timestamps, concatenation, remuxing, and the capture clock |
| Output has no audio or subtitles | Use `ffprobe` and `-map` to select streams explicitly |
| Output file is too large | Adjust CRF, bitrate, resolution, or frame rate |
| Processing is slow | Check the encoding `preset`, software filters, I/O, and hardware acceleration status |

Enable detailed logging:

```bash
ffmpeg -loglevel verbose -i input.mp4 <output-options> output.mp4
```

Save a complete log for issue reporting:

```bash
ffmpeg -report -i input.mp4 <output-options> output.mp4
```

## Rockchip Platform

### 1. Overview

`ffmpeg-rockchip` is an FFmpeg branch extended for Rockchip platforms. It uses MPP (Media Process Platform) for hardware video encoding and decoding, and RGA (2D Raster Graphic Acceleration) for scaling, cropping, rotation, pixel-format conversion, and layer composition.

A typical hardware processing pipeline is:

```text
Input → MPP hardware decoder → DRM_PRIME/DMA-BUF
                                  │
                                  ▼
                           RGA image processing
                                  │
                                  ▼
                           MPP hardware encoder → Output
```

Keeping video frames as `drm_prime` hardware frames reduces data copies between CPU memory and hardware video buffers, lowering CPU usage and DDR bandwidth consumption.

> `ffmpeg-rockchip` is an unofficial FFmpeg branch. Refer to the project's README and license files for usage and open-source licensing requirements.

### 2. Supported Features

MPP hardware decoders:

| FFmpeg name | Format |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `vp8_rkmpp` / `vp9_rkmpp` | VP8/VP9 |
| `av1_rkmpp` | AV1 |
| `mjpeg_rkmpp` | Motion JPEG |
| `mpeg1_rkmpp` / `mpeg2_rkmpp` / `mpeg4_rkmpp` | MPEG Video |
| `h263_rkmpp` | H.263 |

MPP hardware encoders:

| FFmpeg name | Format |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `mjpeg_rkmpp` | Motion JPEG |

RGA hardware filters:

| Filter | Purpose |
| --- | --- |
| `scale_rkrga` | Scaling and pixel-format conversion |
| `vpp_rkrga` | Scaling, cropping, rotation, and flipping |
| `overlay_rkrga` | Video or image composition |

The presence of a codec in the source does not mean that every Rockchip SoC implements the corresponding hardware capability. Maximum resolution, frame rate, bit depth, profile, level, and concurrent stream count depend on the target SoC's datasheet, TRM, and BSP.

### 3. Runtime Requirements

`ffmpeg-rockchip` requires a Rockchip BSP/vendor kernel. The project has mainly been tested with Linux kernels 5.10 and 6.1.

Build and runtime requirements include:

- Rockchip MPP 1.3.9 or later.
- A librga version matching the BSP.
- libdrm with DRM_PRIME/DMA-BUF support.
- Permission to access the MPP, RGA, and DRM device nodes.

Common device nodes include:

```text
/dev/mpp_service
/dev/rga
/dev/dri/
/dev/dma_heap/
```

Check the device nodes:

```bash
ls -l /dev/mpp_service /dev/rga /dev/dri /dev/dma_heap 2>/dev/null
```

The FFmpeg user normally needs to belong to the `video` or `render` group associated with these device nodes:

```bash
id
sudo usermod -aG video,render "$USER"
```

The group change takes effect after logging in again. Production systems should configure permissions with udev rules instead of permanently using `chmod 777`.

### 4. Building and Verifying

Check the dependencies:

```bash
pkg-config --modversion libdrm
pkg-config --modversion rockchip_mpp
pkg-config --modversion librga
```

Get and build the source code:

```bash
git clone https://github.com/nyanmisaka/ffmpeg-rockchip.git
cd ffmpeg-rockchip

./configure \
  --prefix=/usr/local \
  --enable-version3 \
  --enable-libdrm \
  --enable-rkmpp \
  --enable-rkrga \
  --enable-shared \
  --disable-static

make -j$(nproc)
sudo make install
sudo ldconfig
```

Confirm that the expected FFmpeg binary is being used:

```bash
command -v ffmpeg
ffmpeg -version
```

Check the Rockchip hardware features:

```bash
ffmpeg -hide_banner -hwaccels | grep -i rkmpp
ffmpeg -hide_banner -decoders | grep rkmpp
ffmpeg -hide_banner -encoders | grep rkmpp
ffmpeg -hide_banner -filters  | grep rkrga
```

If these entries are missing, the command may be using the distribution's standard FFmpeg binary, or MPP/RGA support may not have been enabled successfully during the build.

### 5. Usage Examples

#### 5.1 Hardware Decoding Test

```bash
ffmpeg \
  -hwaccel rkmpp \
  -hwaccel_output_format drm_prime \
  -i input.mp4 \
  -an -f null -
```

The decoder can also be selected explicitly:

```bash
ffmpeg -c:v h264_rkmpp -i input-h264.mp4 -an -f null -
ffmpeg -c:v hevc_rkmpp -i input-hevc.mp4 -an -f null -
```

#### 5.2 H.264 Hardware Encoding

```bash
ffmpeg \
  -i input.mp4 \
  -c:v h264_rkmpp \
  -rc_mode VBR \
  -b:v 8M \
  -maxrate 10M \
  -g 60 \
  -c:a copy \
  output-h264.mp4
```

#### 5.3 HEVC Hardware Encoding

```bash
ffmpeg \
  -i input.mp4 \
  -c:v hevc_rkmpp \
  -rc_mode VBR \
  -b:v 6M \
  -maxrate 8M \
  -g 60 \
  -c:a copy \
  output-hevc.mp4
```

The H.264 and HEVC encoders support `VBR`, `CBR`, `CQP`, and `AVBR` rate-control modes. Display the complete options supported by the installed build with:

```bash
ffmpeg -hide_banner -h encoder=h264_rkmpp
ffmpeg -hide_banner -h encoder=hevc_rkmpp
```

#### 5.4 Zero-Copy Scaling and Transcoding

```bash
ffmpeg \
  -hwaccel rkmpp \
  -hwaccel_output_format drm_prime \
  -i input.mp4 \
  -vf 'scale_rkrga=w=1920:h=1080:format=nv12' \
  -c:v h264_rkmpp \
  -rc_mode VBR \
  -b:v 8M \
  -c:a copy \
  output-1080p.mp4
```

This command uses MPP decoding, RGA scaling, and MPP encoding. Video frames are passed between hardware blocks through DRM_PRIME/DMA-BUF.

Display the filter help before using other RGA features:

```bash
ffmpeg -hide_banner -h filter=scale_rkrga
ffmpeg -hide_banner -h filter=vpp_rkrga
ffmpeg -hide_banner -h filter=overlay_rkrga
```

### 6. Common Issues

| Symptom | What to check |
| --- | --- |
| `Unknown decoder/encoder` | Use `ffmpeg -version` and `ffmpeg -encoders/-decoders` to verify the active build |
| MPP/RGA device cannot be opened | Check device nodes, user groups, kernel drivers, and the DTS |
| RGA reports `No hw context provided` | Add `-hwaccel_output_format drm_prime` when decoding |
| RGA rejects a format or size | Try a common format such as `nv12`, and check dimension and crop-coordinate alignment |
| CPU usage remains high with hardware acceleration | Check whether software filters are causing `hwdownload` and `hwupload` operations |
| A format cannot be hardware-decoded | Check whether the target SoC supports the codec, bit depth, profile, and resolution |

Display detailed logs:

```bash
ffmpeg -loglevel verbose <other-options>
dmesg | grep -Ei 'mpp|rga|vpu|rkvdec|rkvenc'
```

References:

- [ffmpeg-rockchip](https://github.com/nyanmisaka/ffmpeg-rockchip)
- [Rockchip MPP](https://github.com/rockchip-linux/mpp)
- [Rockchip RGA](https://github.com/airockchip/librga)
