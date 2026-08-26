# Bins and Automatic Pipeline Elements

## 2. Basic Concepts

### 2.6 Bins and Automatic Pipeline Elements

GStreamer provides several bins that build playback paths automatically:

| Element | Purpose |
| --- | --- |
| `decodebin` / `decodebin3` | Automatically select parsers and decoders, and create output pads for different tracks |
| `uridecodebin` / `uridecodebin3` | Automatically select a source based on the URI, then demux and decode the media |
| `playbin` / `playbin3` | Build a complete playback path and automatically select audio and video sinks |

Use automatic pipeline elements first for quick validation. Build the pipeline explicitly when you need to select a specific hardware decoder, control latency, or troubleshoot an issue.
