# Supported Features

## Rockchip Platform

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
