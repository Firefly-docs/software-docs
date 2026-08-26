# 概述

## Rockchip 平台

### 1. 概述

`ffmpeg-rockchip` 是面向 Rockchip 平台扩展的 FFmpeg 分支，使用 MPP（Media Process Platform）完成硬件视频编解码，使用 RGA（2D Raster Graphic Acceleration）完成缩放、裁剪、旋转、像素格式转换和图层叠加。

典型硬件处理链路如下：

```text
输入 → MPP 硬件解码 → DRM_PRIME/DMA-BUF
                         │
                         ▼
                    RGA 图像处理
                         │
                         ▼
                    MPP 硬件编码 → 输出
```

视频帧保持为 `drm_prime` 硬件帧时，可以减少 CPU 内存和硬件视频缓冲区之间的数据复制，降低 CPU 占用和 DDR 带宽开销。

> `ffmpeg-rockchip` 是非官方 FFmpeg 分支。实际使用和开源许可应以项目源码中的 README 和 License 文件为准。
