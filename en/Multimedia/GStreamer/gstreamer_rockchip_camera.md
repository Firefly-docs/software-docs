# Camera Capture, Encoding, and Preview

## 7. Rockchip Platform Support

### 7.6 Camera Capture, Encoding, and Preview

Rockchip platforms still use the standard `v4l2src` element for camera capture. The following pipeline splits one NV12 stream into hardware encoding and display branches:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12' \
  ! tee name=t \
  t. ! queue ! mpph264enc ! h264parse ! filesink location=/data/out.h264 \
  t. ! queue ! waylandsink
```

HDMI-IN is used in the same way as a camera. First find the node named `hdmirx`:

```bash
grep '' /sys/class/video4linux/*/name
```

Then point `v4l2src device=` to the corresponding `/dev/videoX` device.
