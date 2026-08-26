# Rockchip 插件概览

## 7. Rockchip 平台支持

### 7.3 Rockchip 插件概览

| 插件 | 作用 |
| --- | --- |
| `mppvideodec` | 使用 MPP/VPU 解码 H.263、H.264、H.265、VP8、VP9、MPEG、AV1 等视频 |
| `mppjpegdec` | 使用 MPP/VPU 解码 JPEG |
| `mpph264enc` | 使用 MPP/VPU 编码 H.264 |
| `mpph265enc` | 使用 MPP/VPU 编码 H.265 |
| `mppvp8enc` | 使用 MPP/VPU 编码 VP8 |
| `mppjpegenc` | 使用 MPP/VPU 编码 JPEG |
| `kmssrc` | 从 DRM/KMS 对象获取画面，可用于录屏 |
| `rkximagesink` | 通过 X11 和 DRM 进行零拷贝显示，需要独占硬件 Plane |

插件列出的格式不等于所有 Rockchip 芯片都支持，应同时确认芯片 Datasheet。
