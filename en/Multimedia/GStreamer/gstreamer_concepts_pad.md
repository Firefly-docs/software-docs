# Pad

## 2. Basic Concepts

### 2.3 Pad

Elements connect through pads:

- A source pad outputs data.
- A sink pad receives data.
- A static pad exists as soon as the element is created.
- A dynamic pad is created after a media track is detected; this is common with demuxers and `decodebin`.

When a container includes both video and audio, name the demuxer and connect its dynamic pads separately:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! qtdemux name=demux \
  demux.video_0 ! queue ! filesink location=video.bin \
  demux.audio_0 ! queue ! filesink location=audio.bin
```

Pad names may vary with the container and plugin version. Use `gst-inspect-1.0 <element>` to check them. You can also use `demux.` and let GStreamer negotiate the connection according to the media type.
