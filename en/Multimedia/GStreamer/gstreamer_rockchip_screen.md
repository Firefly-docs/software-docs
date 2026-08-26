# Screen Capture and Recording

## 7. Rockchip Platform Support

### 7.8 Screen Capture and Recording

`kmssrc` can capture an image from a DRM/KMS object:

```bash
gst-launch-1.0 kmssrc ! waylandsink
```

Record to a raw H.264 stream:

```bash
gst-launch-1.0 kmssrc sync-fb=0 \
  ! mpph264enc \
  ! h264parse \
  ! filesink location=/tmp/screen.h264
```

Use `connector-id`, `crtc-id`, `plane-id`, or `fb-id` to select the capture object. Query the relevant IDs with `modetest`.
