# MPP 硬件编码

## 7. Rockchip 平台支持

### 7.5 MPP 硬件编码

H.264 编码并封装为 MP4：

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph264enc \
  ! h264parse \
  ! qtmux \
  ! filesink location=h264.mp4
```

H.265 编码并封装为 MP4：

```bash
gst-launch-1.0 videotestsrc num-buffers=600 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=60/1' \
  ! mpph265enc \
  ! h265parse \
  ! qtmux \
  ! filesink location=h265.mp4
```

JPEG 编码：

```bash
gst-launch-1.0 videotestsrc num-buffers=1 \
  ! 'video/x-raw,format=NV12,width=640,height=320' \
  ! mppjpegenc \
  ! filesink location=output.jpg
```

视频编码器常用属性：

| 属性 | 说明 |
| --- | --- |
| `rc-mode` | 码率控制模式：`vbr`、`cbr` 或 `fixqp` |
| `bps` | 目标码率；`fixqp` 模式下忽略 |
| `bps-min`、`bps-max` | 最低和最高码率 |
| `gop` | I 帧间隔；默认 `-1` 时通常按帧率设置 |
| `qp-init`、`qp-min`、`qp-max` | 量化参数，QP 越低通常画质越高、码率越大 |
| `profile`、`level` | H.264/H.265 的 Profile 和 Level |
| `width`、`height`、`rotation` | 可能通过 RGA 完成缩放或旋转 |
| `arm-afbc` | 输入数据为 AFBC 时启用 |

属性和取值应以本机 `gst-inspect-1.0 <encoder>` 为准。
