# Play an RTSP Stream

## 4. General GStreamer Usage

### 4.4 Play an RTSP Stream

Let GStreamer select the decoder automatically:

```bash
gst-launch-1.0 uridecodebin uri=rtsp://192.168.1.100:8554/live \
  ! autovideosink
```

Build an H.264 receive path explicitly:

```bash
gst-launch-1.0 rtspsrc location=rtsp://192.168.1.100:8554/live \
  ! rtph264depay \
  ! h264parse \
  ! decodebin \
  ! autovideosink
```

In a production project, adjust the `rtspsrc` `latency` property and transport protocol for the network conditions.
