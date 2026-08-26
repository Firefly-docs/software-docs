# Play a Local File

## 4. General GStreamer Usage

### 4.2 Play a Local File

The simplest playback method is:

```bash
gst-play-1.0 test.mp4
```

Using `playbin`:

```bash
gst-launch-1.0 playbin uri=file:///absolute/path/test.mp4
```

Using `uridecodebin` with a specified video output:

```bash
gst-launch-1.0 uridecodebin uri=file:///absolute/path/test.mp4 \
  ! autovideosink
```

A file URI must use an absolute path and be written as `file:///path/to/file`.
