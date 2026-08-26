# 屏幕采集和录屏

## 7. Rockchip 平台支持

### 7.8 屏幕采集和录屏

`kmssrc` 可从 DRM/KMS 对象获取画面：

```bash
gst-launch-1.0 kmssrc ! waylandsink
```

录制为 H.264 裸流：

```bash
gst-launch-1.0 kmssrc sync-fb=0 \
  ! mpph264enc \
  ! h264parse \
  ! filesink location=/tmp/screen.h264
```

可通过 `connector-id`、`crtc-id`、`plane-id` 或 `fb-id` 指定采集对象，相关 ID 可用 `modetest` 查询。
