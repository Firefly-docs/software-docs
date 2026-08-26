# 性能调试

## 7. Rockchip 平台支持

### 7.13 性能调试

确认实际使用 MPP Decoder：

```bash
GST_DEBUG='2,*mpp*:4' gst-play-1.0 test.mp4
```

测量解码和显示帧率：

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-play-1.0 --flags=3 \
  --videosink='fpsdisplaysink video-sink=waylandsink signal-fps-measurements=true text-overlay=false sync=false' \
  test.mp4
```

导出 Decoder 输出：

```bash
gst-launch-1.0 filesrc location=test.mp4 \
  ! parsebin \
  ! mppvideodec \
  ! filesink location=/tmp/decoded.yuv
```

不开启 AFBC 时，还可启用 MPP 自带 Dump：

```bash
export mpp_debug=0x400
```

AFBC 数据不能直接使用普通 YUV 播放器查看，需要配套解压工具以及正确的宽、高、格式和模式参数。
