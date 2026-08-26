# Common GStreamer Tools

## 3. Common GStreamer Tools

### 3.1 gst-launch-1.0

Builds a pipeline from the command line:

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

Add `-v` to print Caps negotiation information:

```bash
gst-launch-1.0 -v videotestsrc ! autovideosink
```

### 3.2 gst-play-1.0

Quickly plays a local file or URI:

```bash
gst-play-1.0 test.mp4
gst-play-1.0 https://example.com/test.mp4
```

Common options:

| Option | Description |
| --- | --- |
| `--videosink=<element>` | Specify the video output element |
| `--audiosink=<element>` | Specify the audio output element |
| `--use-playbin3` | Use Playbin3 |
| `--flags=<value>` | Control playback features such as video, audio, and subtitles |

Common `--flags` values:

| Value | Function |
| --- | --- |
| `1` | Video only |
| `2` | Audio only |
| `3` | Video and audio |
| `4` | Subtitles |
| `7` | Video, audio, and subtitles |

### 3.3 gst-inspect-1.0

Queries plugins, elements, pads, Caps, properties, and default values:

```bash
# List all plugins and elements
gst-inspect-1.0

# Inspect a specific element
gst-inspect-1.0 waylandsink
gst-inspect-1.0 v4l2src
```

Plugin capabilities vary with the GStreamer, SDK, and platform versions. Treat properties in this document as a reference and check the output on the target system before use.

### 3.4 gst-discoverer-1.0

Analyzes the container, tracks, and codec formats in a file or URI:

```bash
gst-discoverer-1.0 test.mp4
gst-discoverer-1.0 rtsp://127.0.0.1:8554/live
```

If the system is missing a demuxer, parser, or decoder, this command usually reports the missing component.
