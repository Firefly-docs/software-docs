# Rockchip 显示路径

## 7. Rockchip 平台支持

### 7.9 Rockchip 显示路径

| Sink | 显示环境 | 特点 |
| --- | --- | --- |
| `waylandsink` | Wayland | 由 Wayland/GPU 合成，支持显示区域和层级等属性 |
| `xvimagesink` | X11/XVideo | 通过 X11/XVideo 显示，由 GPU 合成 |
| `kmssink` | DRM/KMS | 直接使用硬件 Plane，需要可用且独占的 Plane |
| `rkximagesink` | X11 + DRM | Rockchip 零拷贝显示插件，需要独占硬件 Plane |

Wayland 指定显示区域：

```bash
gst-launch-1.0 videotestsrc \
  ! waylandsink render-rectangle='<0,0,800,480>'
```

KMS 指定硬件 Plane：

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --videosink='kmssink plane-id=117'
```

查询 DRM Plane：

```bash
cat /sys/kernel/debug/dri/0/state
modetest -p
```

不同 Plane 支持的格式不同。例如部分芯片的 Cluster Plane 支持 AFBC，而 Esmart Plane 不支持。使用 `kmssink` 或 `rkximagesink` 前，应确认目标 Plane 未被其他显示客户端占用。
