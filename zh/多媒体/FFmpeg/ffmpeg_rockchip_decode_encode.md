# 硬件解码与编码示例

## Rockchip 平台

### 5. 使用示例

#### 5.1 硬件解码测试

```bash
ffmpeg \
  -hwaccel rkmpp \
  -hwaccel_output_format drm_prime \
  -i input.mp4 \
  -an -f null -
```

也可以显式指定解码器：

```bash
ffmpeg -c:v h264_rkmpp -i input-h264.mp4 -an -f null -
ffmpeg -c:v hevc_rkmpp -i input-hevc.mp4 -an -f null -
```

#### 5.2 H.264 硬件编码

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

#### 5.3 HEVC 硬件编码

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

H.264 和 HEVC 编码器支持 `VBR`、`CBR`、`CQP` 和 `AVBR` 码率控制。可通过以下命令查看当前版本的完整参数：

```bash
ffmpeg -hide_banner -h encoder=h264_rkmpp
ffmpeg -hide_banner -h encoder=hevc_rkmpp
```

#### 5.4 零拷贝缩放转码

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

此命令使用 MPP 解码、RGA 缩放和 MPP 编码。视频帧通过 DRM_PRIME/DMA-BUF 在硬件模块之间传递。

使用其他 RGA 功能前可查看滤镜帮助：

```bash
ffmpeg -hide_banner -h filter=scale_rkrga
ffmpeg -hide_banner -h filter=vpp_rkrga
ffmpeg -hide_banner -h filter=overlay_rkrga
```
