# MPP Hardware Decoding

## 7. Rockchip Platform Support

### 7.4 MPP Hardware Decoding

Select the decoder automatically:

```bash
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

When `mppvideodec` has a sufficiently high rank and supports the input format, automatic pipeline selection will normally choose the MPP decoder. For troubleshooting, explicitly specify it:

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! waylandsink
```

JPEG hardware decoding:

```bash
gst-launch-1.0 filesrc location=input.jpg \
  ! jpegparse \
  ! mppjpegdec \
  ! filesink location=output.yuv
```

Common `mppvideodec` properties:

| Property | Description |
| --- | --- |
| `arm-afbc` | Output AFBC-compressed data; disabled by default |
| `fast-mode` | Enable MPP Fast Mode; enabled by default and may improve parallel decoding efficiency on some platforms |
| `ignore-error` | Ignore some MPP decoding errors and continue output; enabled by default |
| `format` | Specify the output pixel format; conversion may be performed through RGA |
| `width`, `height` | Specify the output dimensions; scaling may be performed through RGA |
| `rotation` | Rotate by 0/90/180/270 degrees; rotation may be performed through RGA |
| `crop-rectangle` | Crop using `<x,y,width,height>` |
| `dma-feature` | Enable the DMA buffer feature; refer to the current plugin documentation |
