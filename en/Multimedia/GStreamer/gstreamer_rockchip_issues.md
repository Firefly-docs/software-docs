# Common Issues

## 7. Rockchip Platform Support

### 7.14 Common Issues

#### 7.14.1 4K30 Plays Normally but 4K60 Stutters

1. Confirm that `mppvideodec`, rather than a software decoder, is actually being used.
2. Test the frame rate from the decoder to `fakesink` and from the decoder to the display sink separately.
3. Use `--flags=3` to disable subtitles.
4. Check the CPU, GPU, and VPU frequencies, system load, and DDR bandwidth.
5. Compare performance with AFBC when the entire path supports it.
6. Test `sync=false` only when measuring throughput; clock synchronization should normally remain enabled during playback.

#### 7.14.2 High CPU Usage

Common causes include not using MPP hardware decoding, software format conversion, or high processing overhead from subtitles and scaling. Use `GST_DEBUG` and a DOT graph to confirm which decoder was actually created and whether `videoconvert` was inserted.

#### 7.14.3 Playback Stalls or Progress Remains at 0

First try Playbin3:

```bash
gst-play-1.0 --flags=3 --use-playbin3 test.mp4
```

Also use `gst-discoverer-1.0` to check the container, tracks, and missing plugins.

#### 7.14.4 Video Is Displayed but There Is No Audio

First use `aplay` to verify the sound card, and then specify the ALSA device explicitly:

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --audiosink='alsasink device=hw:0,0'
```

#### 7.14.5 Subtitles Cause Stuttering

Subtitle composition may involve image extraction, RGB conversion, blending, and conversion back to the display format. Use `--flags=3` to disable subtitles during performance tests. For high-resolution products, consider drawing subtitles on a separate layer with a UI framework such as Qt.

#### 7.14.6 `v4l2src` Cannot Capture at the Full Frame Rate

First bypass GStreamer to verify the V4L2 driver capability:

```bash
v4l2-ctl -d /dev/video0 \
  --set-fmt-video=width=1920,height=1080,pixelformat=NV12 \
  --stream-mmap=3 --stream-skip=1 --stream-poll
```

If `v4l2-ctl` reaches the full frame rate but GStreamer does not, increase the number of buffers:

```bash
gst-launch-1.0 v4l2src device=/dev/video0 min-buffers=64 \
  ! 'video/x-raw,width=1920,height=1080,format=NV12' \
  ! waylandsink
```

Also confirm that the downstream path is not blocked, and add a `queue` before branches such as encoding and display.

#### 7.14.7 `v4l2src` Format Negotiation Fails

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
gst-inspect-1.0 v4l2src
```

- When the device outputs a raw format such as NV12, NV16, BGRA, or BGRx, connect it to a display or encoding plugin that supports the same Caps.
- When the device outputs JPEG/MJPEG, add `jpegparse ! mppjpegdec`.
- When the upstream and downstream formats do not match, add `videoconvert`, while accounting for the CPU or RGA overhead.

#### 7.14.8 Resolutions Above 4K Cannot Be Captured

Check the limit in `/etc/profile.d/gst.sh`:

```bash
export GST_V4L2SRC_MAX_RESOLUTION=3840x2160
```

If the hardware, driver, and bandwidth all support the required resolution, increase this value as needed and reload the environment.
