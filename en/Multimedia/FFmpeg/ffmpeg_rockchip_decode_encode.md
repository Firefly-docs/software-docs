# Hardware Decode and Encode Examples

## Rockchip Platform

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
