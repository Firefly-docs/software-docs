# AFBC 带宽优化

## 7. Rockchip 平台支持

### 7.10 AFBC 带宽优化

AFBC（Arm Frame Buffer Compression）可降低帧缓冲占用的 DDR 带宽。原指南中 `mppvideodec` 的 AFBC 支持范围包括 H.264、H.265、VP9，以及 NV12、NV12 10-bit、NV16 等输出格式；最终能力取决于芯片和完整显示链路。

为单条 Pipeline 开启 AFBC：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec arm-afbc=true \
  ! waylandsink
```

为自动播放链路设置默认值：

```bash
export GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

检查 Decoder 是否输出 AFBC：

```bash
GST_DEBUG='*mpp*:4' gst-play-1.0 \
  --flags=3 --videosink=waylandsink test.mp4
```

日志出现类似内容时表示已经启用：

```text
applying NV12(AFBC) 1920x1080
```

注意事项：

- Decoder、Sink 和 DRM Plane 必须共同支持 AFBC。
- `kmssink` 和 `rkximagesink` 通常需要选择支持 AFBC 的 Cluster Plane。
- 明显缩放、旋转或特定显示方向下，AFBC 不一定获得更好性能。
- 开启后出现闪烁时，应检查系统性能模式、Plane 能力和缩放链路。
