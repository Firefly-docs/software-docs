# Camera Preview

## 4. General GStreamer Usage

### 4.3 Camera Preview

First query the formats and resolutions supported by the video device:

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
```

Preview with GStreamer:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

If the camera outputs MJPEG, add JPEG decoding:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'image/jpeg,width=1920,height=1080,framerate=30/1' \
  ! jpegparse \
  ! decodebin \
  ! autovideosink
```
