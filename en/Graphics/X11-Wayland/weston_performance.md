# Performance-Related Configuration

Weston in the SDK uses GPU rendering and composition acceleration by default. For platforms without a GPU or with insufficient GPU performance, enable the RGA and Pixman-related Buildroot options so Weston can use RGA for acceleration.

When the SoC supports AFBC, allow Weston to use GPU-based AFBC compressed display formats:

```bash
export WESTON_ALLOW_GBM_MODIFIERS=1
```

When UI performance or DDR bandwidth is insufficient, reduce the UI resolution:

```bash
echo "output:HDMI-A-1:down-scale=0.5" >> /tmp/.weston_drm.conf
```
