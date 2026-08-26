# 性能相关配置

SDK 中 Weston 默认使用 GPU 做渲染合成加速。对于无 GPU 或 GPU 性能不足的平台，可在 Buildroot 配置中启用 RGA 和 Pixman 相关选项，让 Weston 使用 RGA 参与加速。

当芯片支持 AFBC 时，可允许 Weston 使用基于 GPU 的 AFBC 压缩格式显示：

```bash
export WESTON_ALLOW_GBM_MODIFIERS=1
```

当 UI 性能或 DDR 带宽不足时，可降低 UI 分辨率：

```bash
echo "output:HDMI-A-1:down-scale=0.5" >> /tmp/.weston_drm.conf
```
