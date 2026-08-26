# General Debugging and Performance Analysis

## 5. General Debugging and Performance Analysis

### 5.1 Enable Logging

GStreamer log levels are as follows:

| Level | Value |
| --- | --- |
| ERROR | `1` |
| WARNING | `2` |
| FIXME | `3` |
| INFO | `4` |
| DEBUG | `5` |
| LOG | `6` |
| TRACE | `7` |

Set the global log level:

```bash
GST_DEBUG=2 gst-play-1.0 test.mp4
```

Set different levels for different modules:

```bash
GST_DEBUG='2,decodebin:5,v4l2*:4' gst-play-1.0 test.mp4
```

### 5.2 Check the Frame Rate

`fpsdisplaysink` measures the video frame rate and forwards the frames to the actual video sink:

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=autovideosink \
      signal-fps-measurements=true text-overlay=false
```

`sync=false` can be used to test maximum pipeline throughput, but it disables clock synchronization and does not represent normal playback behavior.

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=fakesink \
      signal-fps-measurements=true text-overlay=false sync=false
```

### 5.3 Export a Pipeline Graph

```bash
export GST_DEBUG_DUMP_DOT_DIR=/tmp
gst-play-1.0 test.mp4
ls -l /tmp/*.dot
```

Convert the graph to an image on a host with Graphviz installed:

```bash
dot pipeline.dot -Tpng -o pipeline.png
```

One run may generate multiple DOT files, each representing the pipeline structure in a different state.

### 5.4 General Troubleshooting Procedure

1. Use `gst-inspect-1.0` to verify that the plugin exists and inspect its pads, Caps, and properties.
2. Use `gst-discoverer-1.0` or `v4l2-ctl` to identify the input format.
3. Add `-v` to `gst-launch-1.0` to inspect the negotiated Caps.
4. Reduce a complex pipeline to `Source → Parser/Decoder → fakesink`.
5. Gradually add format conversion, encoding, display, audio, and network branches.
6. Add a `queue` after a `tee`, demuxer, or dynamic pad to rule out branch blocking.
7. Use `GST_DEBUG` to locate plugin-loading, state-transition, and negotiation errors.
8. Use `fpsdisplaysink` to distinguish input, codec, processing, and display bottlenecks.
9. Export a DOT graph to confirm the elements that were actually created and how they are connected.
