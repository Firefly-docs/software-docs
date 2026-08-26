# 摄像头预览

## 4. GStreamer 通用使用方法

### 4.3 摄像头预览

先查询视频设备支持的格式和分辨率：

```bash
v4l2-ctl -d /dev/video0 --list-formats-ext
```

使用 GStreamer 预览：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'video/x-raw,format=NV12,width=1920,height=1080,framerate=30/1' \
  ! autovideosink
```

如果摄像头输出 MJPEG，需要增加 JPEG 解码：

```bash
gst-launch-1.0 v4l2src device=/dev/video0 \
  ! 'image/jpeg,width=1920,height=1080,framerate=30/1' \
  ! jpegparse \
  ! decodebin \
  ! autovideosink
```
