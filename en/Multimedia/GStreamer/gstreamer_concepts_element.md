# Element

## 2. Basic Concepts

### 2.1 Element

An element is the basic functional unit of a pipeline. Elements generally fall into the following categories:

| Category | Common Elements | Purpose |
| --- | --- | --- |
| Source | `filesrc`, `v4l2src`, `videotestsrc`, `rtspsrc` | Generate or read media data |
| Demuxer | `qtdemux`, `matroskademux`, `tsdemux` | Separate audio, video, and subtitles from a container |
| Depayloader | `rtph264depay`, `rtph265depay` | Extract an encoded stream from RTP packets |
| Parser | `h264parse`, `h265parse`, `jpegparse` | Parse and normalize an encoded stream |
| Decoder | `decodebin` or a specific software/hardware decoder | Decode compressed data into raw audio or video |
| Encoder | A specific software/hardware encoder | Encode raw audio or video into a compressed stream |
| Filter | `videoconvert`, `videoscale`, `videoflip` | Convert formats, scale, rotate, or otherwise process data |
| Sink | `autovideosink`, `waylandsink`, `filesink`, `fakesink` | Display, save, or discard data |

Use the following commands to inspect the elements available on the current system:

```bash
gst-inspect-1.0
gst-inspect-1.0 v4l2src
gst-inspect-1.0 h264parse
```
