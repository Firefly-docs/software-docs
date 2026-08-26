# AFBC Bandwidth Optimization

## 7. Rockchip Platform Support

### 7.10 AFBC Bandwidth Optimization

AFBC (Arm Frame Buffer Compression) can reduce the DDR bandwidth used by frame buffers. In the original guide, `mppvideodec` AFBC support includes H.264, H.265, and VP9, with output formats such as NV12, 10-bit NV12, and NV16. The final capability depends on the SoC and the complete display path.

Enable AFBC for a single pipeline:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec arm-afbc=true \
  ! waylandsink
```

Set the default for automatically constructed playback pipelines:

```bash
export GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

Check whether the decoder outputs AFBC:

```bash
GST_DEBUG='*mpp*:4' gst-play-1.0 \
  --flags=3 --videosink=waylandsink test.mp4
```

AFBC is enabled if the log contains a message similar to:

```text
applying NV12(AFBC) 1920x1080
```

Notes:

- The decoder, sink, and DRM plane must all support AFBC.
- `kmssink` and `rkximagesink` normally require a Cluster plane that supports AFBC.
- AFBC does not necessarily improve performance when significant scaling, rotation, or a specific display orientation is used.
- If the display flickers after AFBC is enabled, check the system performance mode, plane capabilities, and scaling path.
