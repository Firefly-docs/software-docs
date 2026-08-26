# 支持的功能

## Rockchip 平台

### 2. 支持的功能

MPP 硬件解码器：

| FFmpeg 名称 | 格式 |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `vp8_rkmpp` / `vp9_rkmpp` | VP8/VP9 |
| `av1_rkmpp` | AV1 |
| `mjpeg_rkmpp` | Motion JPEG |
| `mpeg1_rkmpp` / `mpeg2_rkmpp` / `mpeg4_rkmpp` | MPEG Video |
| `h263_rkmpp` | H.263 |

MPP 硬件编码器：

| FFmpeg 名称 | 格式 |
| --- | --- |
| `h264_rkmpp` | H.264/AVC |
| `hevc_rkmpp` | H.265/HEVC |
| `mjpeg_rkmpp` | Motion JPEG |

RGA 硬件滤镜：

| 滤镜 | 作用 |
| --- | --- |
| `scale_rkrga` | 缩放和像素格式转换 |
| `vpp_rkrga` | 缩放、裁剪、旋转和翻转 |
| `overlay_rkrga` | 视频或图像叠加 |

源码中注册某个编解码器，不代表所有 Rockchip SoC 都具有对应硬件能力。最大分辨率、帧率、位深、Profile、Level 和并发路数应以目标 SoC 的 Datasheet、TRM 和实际 BSP 为准。
