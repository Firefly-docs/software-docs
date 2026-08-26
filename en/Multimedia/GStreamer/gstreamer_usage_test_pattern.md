# Display a Test Pattern

## 4. General GStreamer Usage

This section uses only standard or automatically selected plugins and does not depend on a specific hardware platform. For platform-specific hardware acceleration commands, see the later sections.

### 4.1 Display a Test Pattern

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

Specify the raw video format:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1280,height=720,framerate=30/1' \
  ! autovideosink
```
