# Performance Debugging

## 7. Rockchip Platform Support

### 7.13 Performance Debugging

Confirm that the MPP decoder is actually being used:

```bash
GST_DEBUG='2,*mpp*:4' gst-play-1.0 test.mp4
```

Measure decoding and display frame rates:

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-play-1.0 --flags=3 \
  --videosink='fpsdisplaysink video-sink=waylandsink signal-fps-measurements=true text-overlay=false sync=false' \
  test.mp4
```

Export decoder output:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! filesink location=/tmp/decoded.yuv
```

When AFBC is not enabled, you can also enable the MPP built-in dump function:

```bash
export mpp_debug=0x400
```

AFBC data cannot be viewed directly with a regular YUV player. A corresponding decompression tool and the correct width, height, format, and mode parameters are required.
