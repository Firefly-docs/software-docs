# Linux GStreamer Usage and Platform Support

## 1. Introduction to GStreamer

GStreamer is an open-source, plugin-based multimedia framework for audio and video playback, capture, encoding and decoding, format conversion, network transmission, recording, and media analysis.

Applications connect multiple elements through a pipeline. Data flows from a source, passes through demuxing, parsing, encoding or decoding, and processing, and finally reaches a sink.

```text
File, camera, or network stream
        │
        ▼
     Source
        │
        ▼
 Demuxer / Depayloader
        │
        ▼
      Parser
        │
        ▼
 Encoder / Decoder
        │
        ▼
 Convert / Scale / Filter
        │
        ▼
   Display / File / Network
```

GStreamer's main features include:

- Functionality is assembled through plugins, so applications do not need to handle every container and codec format directly.
- Caps are used for media format negotiation.
- Dynamic pads, multiple audio/video streams, and branched pipelines are supported.
- The same high-level pipeline can use software codecs or hardware-accelerated plugins for different platforms.
- Command-line tools, a C API, and bindings for multiple languages are available.

## 2. Basic Concepts

### 2.1 Element

An element is the basic functional unit of a pipeline. Elements generally fall into the following categories:

| Category | Common Elements | Purpose |
| --- | --- | --- |
| Source | `filesrc`, `v4l2src`, `videotestsrc`, `rtspsrc` | Generate or read media data |
| Demuxer | `qtdemux`, `matroskademux`, `tsdemux` | Separate audio, video, and subtitles from a container |
| Depayloader | `rtph264depay`, `rtph265depay` | Extract an encoded stream from RTP packets |
| Parser | `h264parse`, `h265parse`, `jpegparse` | Parse and normalize an encoded stream |
| Decoder | `decodebin` or a specific software/hardware decoder | Decode compressed data into raw audio or video |
| Encoder | A specific software/hardware encoder | Encode raw audio or video into a compressed stream |
| Filter | `videoconvert`, `videoscale`, `videoflip` | Convert formats, scale, rotate, or otherwise process data |
| Sink | `autovideosink`, `waylandsink`, `filesink`, `fakesink` | Display, save, or discard data |

Use the following commands to inspect the elements available on the current system:

```bash
gst-inspect-1.0
gst-inspect-1.0 v4l2src
gst-inspect-1.0 h264parse
```

### 2.2 Pipeline

On the command line, use `!` to connect elements:

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

This pipeline uses `videotestsrc` to generate a test pattern, and then lets `autovideosink` select a display plugin automatically.

Production applications usually create and control pipelines through the GStreamer API. `gst-launch-1.0` is mainly used to validate designs, reproduce issues, and run performance tests.

### 2.3 Pad

Elements connect through pads:

- A source pad outputs data.
- A sink pad receives data.
- A static pad exists as soon as the element is created.
- A dynamic pad is created after a media track is detected; this is common with demuxers and `decodebin`.

When a container includes both video and audio, name the demuxer and connect its dynamic pads separately:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! qtdemux name=demux \
  demux.video_0 ! queue ! filesink location=video.bin \
  demux.audio_0 ! queue ! filesink location=audio.bin
```

Pad names may vary with the container and plugin version. Use `gst-inspect-1.0 <element>` to check them. You can also use `demux.` and let GStreamer negotiate the connection according to the media type.

### 2.4 Caps and Format Negotiation

Caps describe the media types and parameters supported by a pad, such as pixel format, resolution, and frame rate:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

Common Caps types:

| Caps | Meaning |
| --- | --- |
| `video/x-raw` | Uncompressed video frames |
| `video/x-h264` | H.264 encoded stream |
| `video/x-h265` | H.265 encoded stream |
| `image/jpeg` | JPEG/MJPEG data |
| `audio/x-raw` | Uncompressed audio data |

If the upstream and downstream Caps are incompatible, the pipeline reports negotiation errors such as `not-negotiated`. Check the actual formats and insert a parser, decoder, `videoconvert`, or `videoscale` as required.

### 2.5 Queue, Tee, and Multithreading

`tee` duplicates one data stream into multiple branches, while `queue` creates a separate thread and buffer for each branch.

```bash
gst-launch-1.0 videotestsrc \
  ! tee name=t \
  t. ! queue ! autovideosink \
  t. ! queue ! fakesink
```

A `queue` should normally be added to every branch. Otherwise, if any branch blocks, the entire pipeline may stop.

### 2.6 Bins and Automatic Pipeline Elements

GStreamer provides several bins that build playback paths automatically:

| Element | Purpose |
| --- | --- |
| `decodebin` / `decodebin3` | Automatically select parsers and decoders, and create output pads for different tracks |
| `uridecodebin` / `uridecodebin3` | Automatically select a source based on the URI, then demux and decode the media |
| `playbin` / `playbin3` | Build a complete playback path and automatically select audio and video sinks |

Use automatic pipeline elements first for quick validation. Build the pipeline explicitly when you need to select a specific hardware decoder, control latency, or troubleshoot an issue.

## 3. Common GStreamer Tools

### 3.1 gst-launch-1.0

Builds a pipeline from the command line:

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

Add `-v` to print Caps negotiation information:

```bash
gst-launch-1.0 -v videotestsrc ! autovideosink
```

### 3.2 gst-play-1.0

Quickly plays a local file or URI:

```bash
gst-play-1.0 test.mp4
gst-play-1.0 https://example.com/test.mp4
```

Common options:

| Option | Description |
| --- | --- |
| `--videosink=<element>` | Specify the video output element |
| `--audiosink=<element>` | Specify the audio output element |
| `--use-playbin3` | Use Playbin3 |
| `--flags=<value>` | Control playback features such as video, audio, and subtitles |

Common `--flags` values:

| Value | Function |
| --- | --- |
| `1` | Video only |
| `2` | Audio only |
| `3` | Video and audio |
| `4` | Subtitles |
| `7` | Video, audio, and subtitles |

### 3.3 gst-inspect-1.0

Queries plugins, elements, pads, Caps, properties, and default values:

```bash
# List all plugins and elements
gst-inspect-1.0

# Inspect a specific element
gst-inspect-1.0 waylandsink
gst-inspect-1.0 v4l2src
```

Plugin capabilities vary with the GStreamer, SDK, and platform versions. Treat properties in this document as a reference and check the output on the target system before use.

### 3.4 gst-discoverer-1.0

Analyzes the container, tracks, and codec formats in a file or URI:

```bash
gst-discoverer-1.0 test.mp4
gst-discoverer-1.0 rtsp://127.0.0.1:8554/live
```

If the system is missing a demuxer, parser, or decoder, this command usually reports the missing component.

## 4. General GStreamer Usage

This section uses only standard or automatically selected plugins and does not depend on a specific hardware platform. For platform-specific hardware acceleration commands, see the later sections.

### 4.1 Display a Test Pattern

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

Specify the raw video format:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1280,height=720,framerate=30/1' \
  ! autovideosink
```

### 4.2 Play a Local File

The simplest playback method is:

```bash
gst-play-1.0 test.mp4
```

Using `playbin`:

```bash
gst-launch-1.0 playbin uri=file:///absolute/path/test.mp4
```

Using `uridecodebin` with a specified video output:

```bash
gst-launch-1.0 uridecodebin uri=file:///absolute/path/test.mp4 \
  ! autovideosink
```

A file URI must use an absolute path and be written as `file:///path/to/file`.

### 4.3 Camera Preview

First query the formats and resolutions supported by the video device:

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
```

Preview with GStreamer:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

If the camera outputs MJPEG, add JPEG decoding:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'image/jpeg,width=1920,height=1080,framerate=30/1' \
  ! jpegparse \
  ! decodebin \
  ! autovideosink
```

### 4.4 Play an RTSP Stream

Let GStreamer select the decoder automatically:

```bash
gst-launch-1.0 uridecodebin uri=rtsp://192.168.1.100:8554/live \
  ! autovideosink
```

Build an H.264 receive path explicitly:

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! decodebin \
  ! autovideosink
```

In a production project, adjust the `rtspsrc` `latency` property and transport protocol for the network conditions.

### 4.5 Save Raw Data

`filesrc` reads data from a file, while `filesink` saves data to a file:

```bash
gst-launch-1.0 filesrc location=input.bin \
  ! filesink location=output.bin
```

When processing a raw YUV stream, each buffer should normally correspond to one frame. For 640×320 NV12, one frame is `640 × 320 × 1.5 = 307200` bytes:

```bash
gst-launch-1.0 filesrc location=input_nv12.yuv blocksize=307200 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=30/1' \
  ! fakesink
```

### 4.6 Application Integration

`appsrc` and `appsink` exchange data between an application and a pipeline:

- `appsrc`: the application feeds data into the pipeline, for example, a decrypted proprietary stream.
- `appsink`: the application retrieves data from the pipeline, for example, decoded frames for image processing.

A typical application structure:

```text
Custom input → appsrc → GStreamer Pipeline → appsink → Image algorithm
                                            │
                                            ▼
                         appsrc → Post-processing / Encoding / Display
```

The command line is mainly used for validation. Production applications usually use the GStreamer API to handle buffers, timestamps, EOS, and state changes.

## 5. General Debugging and Performance Analysis

### 5.1 Enable Logging

GStreamer log levels are as follows:

| Level | Value |
| --- | --- |
| ERROR | `1` |
| WARNING | `2` |
| FIXME | `3` |
| INFO | `4` |
| DEBUG | `5` |
| LOG | `6` |
| TRACE | `7` |

Set the global log level:

```bash
GST_DEBUG=2 gst-play-1.0 test.mp4
```

Set different levels for different modules:

```bash
GST_DEBUG='2,decodebin:5,v4l2*:4' gst-play-1.0 test.mp4
```

### 5.2 Check the Frame Rate

`fpsdisplaysink` measures the video frame rate and forwards the frames to the actual video sink:

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=autovideosink \
      signal-fps-measurements=true text-overlay=false
```

`sync=false` can be used to test maximum pipeline throughput, but it disables clock synchronization and does not represent normal playback behavior.

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=fakesink \
      signal-fps-measurements=true text-overlay=false sync=false
```

### 5.3 Export a Pipeline Graph

```bash
export GST_DEBUG_DUMP_DOT_DIR=/tmp
gst-play-1.0 test.mp4
ls -l /tmp/*.dot
```

Convert the graph to an image on a host with Graphviz installed:

```bash
dot pipeline.dot -Tpng -o pipeline.png
```

One run may generate multiple DOT files, each representing the pipeline structure in a different state.

### 5.4 General Troubleshooting Procedure

1. Use `gst-inspect-1.0` to verify that the plugin exists and inspect its pads, Caps, and properties.
2. Use `gst-discoverer-1.0` or `v4l2-ctl` to identify the input format.
3. Add `-v` to `gst-launch-1.0` to inspect the negotiated Caps.
4. Reduce a complex pipeline to `Source → Parser/Decoder → fakesink`.
5. Gradually add format conversion, encoding, display, audio, and network branches.
6. Add a `queue` after a `tee`, demuxer, or dynamic pad to rule out branch blocking.
7. Use `GST_DEBUG` to locate plugin-loading, state-transition, and negotiation errors.
8. Use `fpsdisplaysink` to distinguish input, codec, processing, and display bottlenecks.
9. Export a DOT graph to confirm the elements that were actually created and how they are connected.

## 6. Hardware Platform Support

GStreamer's high-level interfaces and pipeline model are platform-independent, but hardware encoding and decoding, zero-copy paths, memory types, display control, and performance optimizations are generally implemented by platform-specific plugins.

Each platform support section should consistently include the following information:

1. Platform architecture and plugin source.
2. Supported operating systems and GStreamer versions.
3. Hardware decoders and encoders.
4. Camera, display, and zero-copy paths.
5. Platform-specific environment variables.
6. Build and deployment methods.
7. Platform limitations and common issues.

Current platform status in this document:

| Platform | Support Status | Main Plugins/Interfaces |
| --- | --- | --- |
| Rockchip | Documented | MPP, RGA, DRM/KMS, `gstreamer-rockchip` |
| Other platforms | To be added |  |

## 7. Rockchip Platform Support

### 7.1 Rockchip Multimedia Architecture

The Rockchip Linux SDK connects GStreamer to MPP (Media Process Platform) through `gstreamer-rockchip`. MPP then invokes the VPU driver to perform hardware encoding and decoding.

```text
GStreamer application
      │
      ▼
gstreamer-rockchip plugin
      │
      ▼
Rockchip MPP
      │
      ▼
VPU driver and hardware codecs
```

Decoded images can be displayed through Wayland, X11, or DRM/KMS. Some paths can also use DMA buffers, AFBC, and hardware planes to reduce memory copies and DDR bandwidth usage.

### 7.2 Software Versions and Source Locations

Reference versions covered by the original guide:

| System | GStreamer Version |
| --- | --- |
| Buildroot | 1.22.x |
| Debian 10 (Buster) | 1.14.4 |
| Debian 11 (Bullseye) | 1.18.5 |
| Ubuntu 22 | 1.20.3 |
| Yocto | 1.20.x |

Source locations:

```text
<SDK>/external/gstreamer-rockchip
<SDK>/buildroot/package/gstreamer1
```

Check the Rockchip plugins:

```bash
gst-inspect-1.0 rockchipmpp
gst-inspect-1.0 mppvideodec
gst-inspect-1.0 mpph264enc
gst-inspect-1.0 mpph265enc
gst-inspect-1.0 mppjpegenc
```

### 7.3 Rockchip Plugin Overview

| Plugin | Purpose |
| --- | --- |
| `mppvideodec` | Use MPP/VPU to decode H.263, H.264, H.265, VP8, VP9, MPEG, AV1, and other video formats |
| `mppjpegdec` | Use MPP/VPU to decode JPEG |
| `mpph264enc` | Use MPP/VPU to encode H.264 |
| `mpph265enc` | Use MPP/VPU to encode H.265 |
| `mppvp8enc` | Use MPP/VPU to encode VP8 |
| `mppjpegenc` | Use MPP/VPU to encode JPEG |
| `kmssrc` | Capture an image from a DRM/KMS object; can be used for screen recording |
| `rkximagesink` | Provide zero-copy display through X11 and DRM; requires exclusive access to a hardware plane |

A format listed by a plugin is not necessarily supported by every Rockchip SoC. Also verify the SoC datasheet.

### 7.4 MPP Hardware Decoding

Select the decoder automatically:

```bash
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

When `mppvideodec` has a sufficiently high rank and supports the input format, automatic pipeline selection will normally choose the MPP decoder. For troubleshooting, explicitly specify it:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! waylandsink
```

JPEG hardware decoding:

```bash
gst-launch-1.0 filesrc location=input.jpg \
  ! jpegparse \
  ! mppjpegdec \
  ! filesink location=output.yuv
```

Common `mppvideodec` properties:

| Property | Description |
| --- | --- |
| `arm-afbc` | Output AFBC-compressed data; disabled by default |
| `fast-mode` | Enable MPP Fast Mode; enabled by default and may improve parallel decoding efficiency on some platforms |
| `ignore-error` | Ignore some MPP decoding errors and continue output; enabled by default |
| `format` | Specify the output pixel format; conversion may be performed through RGA |
| `width`, `height` | Specify the output dimensions; scaling may be performed through RGA |
| `rotation` | Rotate by 0/90/180/270 degrees; rotation may be performed through RGA |
| `crop-rectangle` | Crop using `<x,y,width,height>` |
| `dma-feature` | Enable the DMA buffer feature; refer to the current plugin documentation |

### 7.5 MPP Hardware Encoding

Encode H.264 and mux it into MP4:

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph264enc \
  ! h264parse \
  ! qtmux \
  ! filesink location=h264.mp4
```

Encode H.265 and mux it into MP4:

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph265enc \
  ! h265parse \
  ! qtmux \
  ! filesink location=h265.mp4
```

JPEG encoding:

```bash
gst-launch-1.0 videotestsrc num-buffers=1 \
  ! 'video/x-raw,format=NV12,width=640,height=320' \
  ! mppjpegenc \
  ! filesink location=output.jpg
```

Common video encoder properties:

| Property | Description |
| --- | --- |
| `rc-mode` | Bitrate control mode: `vbr`, `cbr`, or `fixqp` |
| `bps` | Target bitrate; ignored in `fixqp` mode |
| `bps-min`, `bps-max` | Minimum and maximum bitrates |
| `gop` | I-frame interval; when the default is `-1`, it is normally set according to the frame rate |
| `qp-init`, `qp-min`, `qp-max` | Quantization parameters; a lower QP generally means higher quality and a higher bitrate |
| `profile`, `level` | H.264/H.265 profile and level |
| `width`, `height`, `rotation` | Scaling or rotation may be performed through RGA |
| `arm-afbc` | Enable when the input data is AFBC |

Check properties and their accepted values with `gst-inspect-1.0 <encoder>` on the target system.

### 7.6 Camera Capture, Encoding, and Preview

Rockchip platforms still use the standard `v4l2src` element for camera capture. The following pipeline splits one NV12 stream into hardware encoding and display branches:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12' \
  ! tee name=t \
  t. ! queue ! mpph264enc ! h264parse ! filesink location=/data/out.h264 \
  t. ! queue ! waylandsink
```

HDMI-IN is used in the same way as a camera. First find the node named `hdmirx`:

```bash
grep '' /sys/class/video4linux/*/name
```

Then point `v4l2src device=` to the corresponding `/dev/videoX` device.

### 7.7 RTSP Hardware Decoding

H.264 RTSP stream:

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! mppvideodec \
  ! waylandsink
```

For an H.265 stream, use `rtph265depay ! h265parse ! mppvideodec`.

### 7.8 Screen Capture and Recording

`kmssrc` can capture an image from a DRM/KMS object:

```bash
gst-launch-1.0 kmssrc ! waylandsink
```

Record to a raw H.264 stream:

```bash
gst-launch-1.0 kmssrc sync-fb=0 \
  ! mpph264enc \
  ! h264parse \
  ! filesink location=/tmp/screen.h264
```

Use `connector-id`, `crtc-id`, `plane-id`, or `fb-id` to select the capture object. Query the relevant IDs with `modetest`.

### 7.9 Rockchip Display Paths

| Sink | Display Environment | Characteristics |
| --- | --- | --- |
| `waylandsink` | Wayland | Composited by Wayland/GPU; supports properties such as display area and layer |
| `xvimagesink` | X11/XVideo | Displays through X11/XVideo and is composited by the GPU |
| `kmssink` | DRM/KMS | Uses a hardware plane directly; requires an available, exclusively owned plane |
| `rkximagesink` | X11 + DRM | Rockchip zero-copy display plugin; requires exclusive access to a hardware plane |

Specify the display area with Wayland:

```bash
gst-launch-1.0 videotestsrc \
  ! waylandsink render-rectangle='<0,0,800,480>'
```

Specify a hardware plane with KMS:

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --videosink='kmssink plane-id=117'
```

Query DRM planes:

```bash
cat /sys/kernel/debug/dri/0/state
modetest -p
```

Different planes support different formats. For example, on some SoCs, Cluster planes support AFBC while Esmart planes do not. Before using `kmssink` or `rkximagesink`, confirm that the target plane is not occupied by another display client.

### 7.10 AFBC Bandwidth Optimization

AFBC (Arm Frame Buffer Compression) can reduce the DDR bandwidth used by frame buffers. In the original guide, `mppvideodec` AFBC support includes H.264, H.265, and VP9, with output formats such as NV12, 10-bit NV12, and NV16. The final capability depends on the SoC and the complete display path.

Enable AFBC for a single pipeline:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec arm-afbc=true \
  ! waylandsink
```

Set the default for automatically constructed playback pipelines:

```bash
export GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

Check whether the decoder outputs AFBC:

```bash
GST_DEBUG='*mpp*:4' gst-play-1.0 \
  --flags=3 --videosink=waylandsink test.mp4
```

AFBC is enabled if the log contains a message similar to:

```text
applying NV12(AFBC) 1920x1080
```

Notes:

- The decoder, sink, and DRM plane must all support AFBC.
- `kmssink` and `rkximagesink` normally require a Cluster plane that supports AFBC.
- AFBC does not necessarily improve performance when significant scaling, rotation, or a specific display orientation is used.
- If the display flickers after AFBC is enabled, check the system performance mode, plane capabilities, and scaling path.

### 7.11 RGA Image Processing

The Rockchip SDK can use RGA for `videoconvert`, `videoscale`, and `videoflip`, as well as for format conversion, scaling, cropping, and rotation in MPP plugins.

Common switches:

```bash
export GST_VIDEO_CONVERT_USE_RGA=1
export GST_VIDEO_FLIP_USE_RGA=1
```

Format conversion:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12' \
  ! videoconvert \
  ! 'video/x-raw,format=BGRA' \
  ! waylandsink
```

Rotation:

```bash
gst-launch-1.0 videotestsrc \
  ! videoflip video-direction=90r \
  ! waylandsink
```

> The original guide notes that the RGA path may malfunction on some platforms and software versions, including certain RK3588 releases, and should not be relied on by default without validation. Before enabling it, confirm the SDK fix status and test combinations of pixel format, stride, resolution, rotation, and scaling. If an issue occurs, unset the RGA environment variables or avoid the implicit RGA paths triggered by MPP plugin properties such as `format`, `width`, `height`, and `rotation`.

### 7.12 Environment Variables

The Rockchip SDK usually configures the relevant environment variables centrally in `/etc/profile.d/gst.sh`.

| Environment Variable | Purpose |
| --- | --- |
| `GST_MPP_DEC_DISABLE_NV12_10=1` | Disable direct 10-bit NV12 output and attempt to convert it to NV12 |
| `GST_MPP_DEC_DISABLE_NV16_10=1` | Disable direct 10-bit NV16 output and attempt to convert it to NV12 |
| `GST_MPP_VIDEODEC_DEFAULT_FORMAT=NV12` | Make the MPP decoder output NV12 by default; this may invoke RGA |
| `GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1` | Enable AFBC output from the MPP decoder by default |
| `GST_V4L2_PREFERRED_FOURCC=...` | Set the preferred order of V4L2 pixel formats |
| `GST_VIDEO_CONVERT_PREFERRED_FORMAT=...` | Set the preferred order of `videoconvert` output formats |
| `GST_V4L2SRC_MAX_RESOLUTION=3840x2160` | Limit the maximum resolution that `v4l2src` can negotiate |
| `GST_V4L2_MIN_BUFS=64` | Set the minimum number of V4L2 driver buffers |
| `GST_VIDEO_CONVERT_USE_RGA=1` | Enable the RGA path for format conversion and scaling |
| `GST_VIDEO_FLIP_USE_RGA=1` | Enable the RGA path for rotation and flipping |

Set only the variables required by the application, and record the environment when documenting an issue:

```bash
env | grep '^GST_' | sort
```

### 7.13 Performance Debugging

Confirm that the MPP decoder is actually being used:

```bash
GST_DEBUG='2,*mpp*:4' gst-play-1.0 test.mp4
```

Measure decoding and display frame rates:

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-play-1.0 --flags=3 \
  --videosink='fpsdisplaysink video-sink=waylandsink signal-fps-measurements=true text-overlay=false sync=false' \
  test.mp4
```

Export decoder output:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! filesink location=/tmp/decoded.yuv
```

When AFBC is not enabled, you can also enable the MPP built-in dump function:

```bash
export mpp_debug=0x400
```

AFBC data cannot be viewed directly with a regular YUV player. A corresponding decompression tool and the correct width, height, format, and mode parameters are required.

### 7.14 Common Issues

#### 7.14.1 4K30 Plays Normally but 4K60 Stutters

1. Confirm that `mppvideodec`, rather than a software decoder, is actually being used.
2. Test the frame rate from the decoder to `fakesink` and from the decoder to the display sink separately.
3. Use `--flags=3` to disable subtitles.
4. Check the CPU, GPU, and VPU frequencies, system load, and DDR bandwidth.
5. Compare performance with AFBC when the entire path supports it.
6. Test `sync=false` only when measuring throughput; clock synchronization should normally remain enabled during playback.

#### 7.14.2 High CPU Usage

Common causes include not using MPP hardware decoding, software format conversion, or high processing overhead from subtitles and scaling. Use `GST_DEBUG` and a DOT graph to confirm which decoder was actually created and whether `videoconvert` was inserted.

#### 7.14.3 Playback Stalls or Progress Remains at 0

First try Playbin3:

```bash
gst-play-1.0 --flags=3 --use-playbin3 test.mp4
```

Also use `gst-discoverer-1.0` to check the container, tracks, and missing plugins.

#### 7.14.4 Video Is Displayed but There Is No Audio

First use `aplay` to verify the sound card, and then specify the ALSA device explicitly:

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --audiosink='alsasink device=hw:0,0'
```

#### 7.14.5 Subtitles Cause Stuttering

Subtitle composition may involve image extraction, RGB conversion, blending, and conversion back to the display format. Use `--flags=3` to disable subtitles during performance tests. For high-resolution products, consider drawing subtitles on a separate layer with a UI framework such as Qt.

#### 7.14.6 `v4l2src` Cannot Capture at the Full Frame Rate

First bypass GStreamer to verify the V4L2 driver capability:

```bash
v4l2-ctl -d /dev/video0 \
  --set-fmt-video=width=1920,height=1080,pixelformat=NV12 \
  --stream-mmap=3 --stream-skip=1 --stream-poll
```

If `v4l2-ctl` reaches the full frame rate but GStreamer does not, increase the number of buffers:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 min-buffers=64 \
  ! 'video/x-raw,width=1920,height=1080,format=NV12' \
  ! waylandsink
```

Also confirm that the downstream path is not blocked, and add a `queue` before branches such as encoding and display.

#### 7.14.7 `v4l2src` Format Negotiation Fails

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
gst-inspect-1.0 v4l2src
```

- When the device outputs a raw format such as NV12, NV16, BGRA, or BGRx, connect it to a display or encoding plugin that supports the same Caps.
- When the device outputs JPEG/MJPEG, add `jpegparse ! mppjpegdec`.
- When the upstream and downstream formats do not match, add `videoconvert`, while accounting for the CPU or RGA overhead.

#### 7.14.8 Resolutions Above 4K Cannot Be Captured

Check the limit in `/etc/profile.d/gst.sh`:

```bash
export GST_V4L2SRC_MAX_RESOLUTION=3840x2160
```

If the hardware, driver, and bandwidth all support the required resolution, increase this value as needed and reload the environment.
