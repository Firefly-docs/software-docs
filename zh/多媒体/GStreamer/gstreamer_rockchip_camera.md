# 摄像头采集、编码和预览

## 7. Rockchip 平台支持

### 7.6 摄像头采集、编码和预览

Rockchip 平台仍通过标准 `v4l2src` 采集摄像头。下面将一路 NV12 数据分为硬件编码和显示两路：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12' \
  ! tee name=t \
  t. ! queue ! mpph264enc ! h264parse ! filesink location=/data/out.h264 \
  t. ! queue ! waylandsink
```

HDMI-IN 的用法与摄像头相同。可先查找名称为 `hdmirx` 的节点：

```bash
grep '' /sys/class/video4linux/*/name
```

再将 `v4l2src device=` 指向对应 `/dev/videoX`。
