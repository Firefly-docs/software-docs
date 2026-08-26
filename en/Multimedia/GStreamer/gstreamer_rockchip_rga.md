# RGA Image Processing

## 7. Rockchip Platform Support

### 7.11 RGA Image Processing

The Rockchip SDK can use RGA for `videoconvert`, `videoscale`, and `videoflip`, as well as for format conversion, scaling, cropping, and rotation in MPP plugins.

Common switches:

```bash
export GST_VIDEO_CONVERT_USE_RGA=1
export GST_VIDEO_FLIP_USE_RGA=1
```

Format conversion:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12' \
  ! videoconvert \
  ! 'video/x-raw,format=BGRA' \
  ! waylandsink
```

Rotation:

```bash
gst-launch-1.0 videotestsrc \
  ! videoflip video-direction=90r \
  ! waylandsink
```

> The original guide notes that the RGA path may malfunction on some platforms and software versions, including certain RK3588 releases, and should not be relied on by default without validation. Before enabling it, confirm the SDK fix status and test combinations of pixel format, stride, resolution, rotation, and scaling. If an issue occurs, unset the RGA environment variables or avoid the implicit RGA paths triggered by MPP plugin properties such as `format`, `width`, `height`, and `rotation`.
