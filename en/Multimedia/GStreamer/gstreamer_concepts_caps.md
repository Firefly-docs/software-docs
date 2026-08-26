# Caps and Format Negotiation

## 2. Basic Concepts

### 2.4 Caps and Format Negotiation

Caps describe the media types and parameters supported by a pad, such as pixel format, resolution, and frame rate:

```bash
gst-launch-1.0 videotestsrc \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

Common Caps types:

| Caps | Meaning |
| --- | --- |
| `video/x-raw` | Uncompressed video frames |
| `video/x-h264` | H.264 encoded stream |
| `video/x-h265` | H.265 encoded stream |
| `image/jpeg` | JPEG/MJPEG data |
| `audio/x-raw` | Uncompressed audio data |

If the upstream and downstream Caps are incompatible, the pipeline reports negotiation errors such as `not-negotiated`. Check the actual formats and insert a parser, decoder, `videoconvert`, or `videoscale` as required.
