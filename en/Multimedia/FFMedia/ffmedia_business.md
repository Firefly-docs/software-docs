# Application Scenarios and Module Selection

When choosing FFMedia modules, first determine where the data comes from, what processing it needs, and where the result should go. Common combinations include:

| Goal | Input | Processing | Output |
| --- | --- | --- | --- |
| Camera preview | `cam` | `rga` (optional) | `drm-display` or `renderer-video` |
| Network stream playback | `rtsp-client` or `rtmp-client` | `mpp-dec` | Display, recording, or re-encoding |
| Local playback | `file-reader` or `ffmpeg-demux` | `mpp-dec`, `rga` | Display or audio playback |
| Camera streaming | `cam` | `mpp-enc` | `rtsp-server`, `rtmp-server`, or a network muxer |
| Video transcoding | `file-reader` or `ffmpeg-demux` | `mpp-dec`, `rga`, `mpp-enc` | `file-writer` or `ffmpeg-mux` |
| Multi-channel stitching | Multiple inputs and decoders | `video-stack` | Display, recording, or streaming |
| AI video analysis | File, network stream, or Camera | `mpp-dec`, `inference` | OSD, display, recording, or streaming |
| Application-owned data | `ModuleAppSource` or `mem-reader` | Custom processing | Any VO output module |

## Before You Start

- Confirm that the target board provides the required MPP, RGA, DRM, Camera, ALSA, or RKNN runtime environment.
- Confirm that the input format, resolution, and frame rate are supported by the device.
- Match the package architecture, Python wheel, and target system.
- Check whether multi-channel encoding and decoding require a higher file descriptor limit.
- Use the current package's `modules` and `params` output as the source of truth for module availability and parameter names.
