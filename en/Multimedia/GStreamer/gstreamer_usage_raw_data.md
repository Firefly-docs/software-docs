# Save Raw Data

## 4. General GStreamer Usage

### 4.5 Save Raw Data

`filesrc` reads data from a file, while `filesink` saves data to a file:

```bash
gst-launch-1.0 filesrc location=input.bin \
  ! filesink location=output.bin
```

When processing a raw YUV stream, each buffer should normally correspond to one frame. For 640×320 NV12, one frame is `640 × 320 × 1.5 = 307200` bytes:

```bash
gst-launch-1.0 filesrc location=input_nv12.yuv blocksize=307200 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=30/1' \
  ! fakesink
```
