# MPP 硬件解码

## 7. Rockchip 平台支持

### 7.4 MPP 硬件解码

自动选择 Decoder：

```bash
gst-play-1.0 --flags=3 --videosink=waylandsink test.mp4
```

当 `mppvideodec` 的 Rank 足够高且支持输入格式时，自动选路通常会选择 MPP Decoder。排障时建议显式指定：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! waylandsink
```

JPEG 硬件解码：

```bash
gst-launch-1.0 filesrc location=input.jpg \
  ! jpegparse \
  ! mppjpegdec \
  ! filesink location=output.yuv
```

`mppvideodec` 常用属性：

| 属性 | 说明 |
| --- | --- |
| `arm-afbc` | 输出 AFBC 压缩数据，默认关闭 |
| `fast-mode` | 开启 MPP Fast Mode，默认开启；部分平台可提高并行解码效率 |
| `ignore-error` | 忽略部分 MPP 解码错误并继续输出，默认开启 |
| `format` | 指定输出像素格式，可能通过 RGA 转换 |
| `width`、`height` | 指定输出尺寸，可能通过 RGA 缩放 |
| `rotation` | 旋转 0/90/180/270 度，可能通过 RGA 完成 |
| `crop-rectangle` | 按 `<x,y,width,height>` 裁剪 |
| `dma-feature` | 启用 DMA Buffer 特性；以当前插件说明为准 |
