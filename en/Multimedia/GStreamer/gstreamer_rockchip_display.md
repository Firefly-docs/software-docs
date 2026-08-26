# Rockchip Display Paths

## 7. Rockchip Platform Support

### 7.9 Rockchip Display Paths

| Sink | Display Environment | Characteristics |
| --- | --- | --- |
| `waylandsink` | Wayland | Composited by Wayland/GPU; supports properties such as display area and layer |
| `xvimagesink` | X11/XVideo | Displays through X11/XVideo and is composited by the GPU |
| `kmssink` | DRM/KMS | Uses a hardware plane directly; requires an available, exclusively owned plane |
| `rkximagesink` | X11 + DRM | Rockchip zero-copy display plugin; requires exclusive access to a hardware plane |

Specify the display area with Wayland:

```bash
gst-launch-1.0 videotestsrc \
  ! waylandsink render-rectangle='<0,0,800,480>'
```

Specify a hardware plane with KMS:

```bash
gst-play-1.0 --flags=3 test.mp4 \
  --videosink='kmssink plane-id=117'
```

Query DRM planes:

```bash
cat /sys/kernel/debug/dri/0/state
modetest -p
```

Different planes support different formats. For example, on some SoCs, Cluster planes support AFBC while Esmart planes do not. Before using `kmssink` or `rkximagesink`, confirm that the target plane is not occupied by another display client.
