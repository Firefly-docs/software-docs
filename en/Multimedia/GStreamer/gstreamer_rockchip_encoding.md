# MPP Hardware Encoding

## 7. Rockchip Platform Support

### 7.5 MPP Hardware Encoding

Encode H.264 and mux it into MP4:

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph264enc \
  ! h264parse \
  ! qtmux \
  ! filesink location=h264.mp4
```

Encode H.265 and mux it into MP4:

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph265enc \
  ! h265parse \
  ! qtmux \
  ! filesink location=h265.mp4
```

JPEG encoding:

```bash
gst-launch-1.0 videotestsrc num-buffers=1 \
  ! 'video/x-raw,format=NV12,width=640,height=320' \
  ! mppjpegenc \
  ! filesink location=output.jpg
```

Common video encoder properties:

| Property | Description |
| --- | --- |
| `rc-mode` | Bitrate control mode: `vbr`, `cbr`, or `fixqp` |
| `bps` | Target bitrate; ignored in `fixqp` mode |
| `bps-min`, `bps-max` | Minimum and maximum bitrates |
| `gop` | I-frame interval; when the default is `-1`, it is normally set according to the frame rate |
| `qp-init`, `qp-min`, `qp-max` | Quantization parameters; a lower QP generally means higher quality and a higher bitrate |
| `profile`, `level` | H.264/H.265 profile and level |
| `width`, `height`, `rotation` | Scaling or rotation may be performed through RGA |
| `arm-afbc` | Enable when the input data is AFBC |

Check properties and their accepted values with `gst-inspect-1.0 <encoder>` on the target system.
