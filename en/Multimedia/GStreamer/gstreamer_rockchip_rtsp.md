# RTSP Hardware Decoding

## 7. Rockchip Platform Support

### 7.7 RTSP Hardware Decoding

H.264 RTSP stream:

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! mppvideodec \
  ! waylandsink
```

For an H.265 stream, use `rtph265depay ! h265parse ! mppvideodec`.
