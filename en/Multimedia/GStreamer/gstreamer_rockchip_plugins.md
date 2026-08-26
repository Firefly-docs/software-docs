# Rockchip Plugin Overview

## 7. Rockchip Platform Support

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
