# 环境变量

## 7. Rockchip 平台支持

### 7.12 环境变量

Rockchip SDK 通常在 `/etc/profile.d/gst.sh` 中集中设置相关环境变量。

| 环境变量 | 作用 |
| --- | --- |
| `GST_MPP_DEC_DISABLE_NV12_10=1` | 禁止直接输出 NV12 10-bit，并尝试转换为 NV12 |
| `GST_MPP_DEC_DISABLE_NV16_10=1` | 禁止直接输出 NV16 10-bit，并尝试转换为 NV12 |
| `GST_MPP_VIDEODEC_DEFAULT_FORMAT=NV12` | 令 MPP Decoder 默认输出 NV12，可能触发 RGA |
| `GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1` | 默认启用 MPP Decoder 的 AFBC 输出 |
| `GST_V4L2_PREFERRED_FOURCC=...` | 设置 V4L2 首选像素格式顺序 |
| `GST_VIDEO_CONVERT_PREFERRED_FORMAT=...` | 设置 `videoconvert` 首选输出格式顺序 |
| `GST_V4L2SRC_MAX_RESOLUTION=3840x2160` | 限制 `v4l2src` 可协商的最大分辨率 |
| `GST_V4L2_MIN_BUFS=64` | 设置 V4L2 驱动最小缓冲数量 |
| `GST_VIDEO_CONVERT_USE_RGA=1` | 对格式转换和缩放启用 RGA 路径 |
| `GST_VIDEO_FLIP_USE_RGA=1` | 对旋转和翻转启用 RGA 路径 |

建议只设置业务需要的变量，并在问题记录中保存当时的环境：

```bash
env | grep '^GST_' | sort
```
