# 常见问题

## 7. Rockchip 平台支持

### 7.14 常见问题

#### 7.14.1 4K30 正常，4K60 卡顿

1. 确认实际使用 `mppvideodec`，而不是软件 Decoder。
2. 分别测试 Decoder 到 `fakesink` 和 Decoder 到显示 Sink 的帧率。
3. 使用 `--flags=3` 关闭字幕。
4. 检查 CPU、GPU、VPU 频率、系统负载和 DDR 带宽。
5. 在完整链路支持时对比测试 AFBC。
6. 仅定位吞吐量时测试 `sync=false`；正常播放一般保留同步。

#### 7.14.2 CPU 占用较高

常见原因是未使用 MPP 硬件解码、发生软件格式转换，或者字幕、缩放等处理开销较高。应结合 `GST_DEBUG` 和 DOT 图确认实际创建的 Decoder，以及是否插入了 `videoconvert`。

#### 7.14.3 播放卡住或进度为 0

可先尝试 Playbin3：

```bash
gst-play-1.0 --flags=3 --use-playbin3 test.mp4
```

同时使用 `gst-discoverer-1.0` 检查容器、轨道和缺失插件。

#### 7.14.4 有画面但没有声音

先用 `aplay` 验证声卡，再显式指定 ALSA 设备：

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --audiosink='alsasink device=hw:0,0'
```

#### 7.14.5 字幕导致卡顿

字幕合成可能包含图像截取、RGB 转换、混合和回转等操作。性能测试时可用 `--flags=3` 关闭字幕；高分辨率产品可考虑由 Qt 等 UI 框架在独立图层绘制字幕。

#### 7.14.6 `v4l2src` 无法满帧采集

先绕过 GStreamer 验证 V4L2 驱动能力：

```bash
v4l2-ctl -d /dev/video0 \
  --set-fmt-video=width=1920,height=1080,pixelformat=NV12 \
  --stream-mmap=3 --stream-skip=1 --stream-poll
```

如果 `v4l2-ctl` 满帧而 GStreamer 不满帧，可增加缓冲：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 min-buffers=64 \
  ! 'video/x-raw,width=1920,height=1080,format=NV12' \
  ! waylandsink
```

还应确认下游没有阻塞，并在编码、显示等分支前增加 `queue`。

#### 7.14.7 `v4l2src` 格式协商失败

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
gst-inspect-1.0 v4l2src
```

- 输出 NV12、NV16、BGRA、BGRx 等裸流时，可连接支持相同 Caps 的显示或编码插件。
- 输出 JPEG/MJPEG 时，应增加 `jpegparse ! mppjpegdec`。
- 上下游格式不匹配时可增加 `videoconvert`，但应关注 CPU 或 RGA 开销。

#### 7.14.8 无法采集 4K 以上分辨率

检查 `/etc/profile.d/gst.sh` 中的限制：

```bash
export GST_V4L2SRC_MAX_RESOLUTION=3840x2160
```

如果硬件、驱动和带宽均支持，可按需求提高该值并重新加载环境。
