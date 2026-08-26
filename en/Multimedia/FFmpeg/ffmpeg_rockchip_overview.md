# Overview

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
