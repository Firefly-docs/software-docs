# RGA 图像处理

## 7. Rockchip 平台支持

### 7.11 RGA 图像处理

Rockchip SDK 可让 `videoconvert`、`videoscale`、`videoflip`，以及 MPP 插件的格式转换、缩放、裁剪和旋转使用 RGA。

常见开关：

```bash
export GST_VIDEO_CONVERT_USE_RGA=1
export GST_VIDEO_FLIP_USE_RGA=1
```

格式转换：

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12' \
  ! videoconvert \
  ! 'video/x-raw,format=BGRA' \
  ! waylandsink
```

旋转：

```bash
gst-launch-1.0 videotestsrc \
  ! videoflip video-direction=90r \
  ! waylandsink
```

> 原指南指出，部分平台和软件版本（包括部分 RK3588 版本）的 RGA 路径可能异常，不建议在未验证时默认依赖。启用前应确认 SDK 修复状态，并测试像素格式、Stride、分辨率、旋转和缩放组合。出现异常时，可取消 RGA 环境变量，或避免使用 MPP 插件的 `format`、`width`、`height` 和 `rotation` 等隐式 RGA 路径。
