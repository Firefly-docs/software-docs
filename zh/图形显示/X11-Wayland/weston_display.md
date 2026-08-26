# 常用显示配置

Weston 的颜色格式、旋转、分辨率和缩放可通过 `weston.ini` 的 `[core]` 或 `[output]` 段配置。Buildroot SDK 内 Weston 默认显示格式为 `ARGB8888`，低性能平台可按需改为 `rgb565`：

```ini
[core]
gbm-format=rgb565
```

单独配置某个屏幕：

```ini
[output]
name=LVDS-1
gbm-format=rgb565
transform=rotate-90
mode=1280x800
scale=2
```

也可以通过动态配置文件运行时调整：

```bash
echo "output:all:rotate90" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:mode=800x600" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:size=1920x1080" > /tmp/.weston_drm.conf
```

如需在物理分辨率不变的情况下统一缩放 UI，可在 `/etc/profile.d/weston.sh` 中配置：

```bash
export WESTON_DRM_VIRTUAL_SIZE=1024x768
```

缩放时如果硬件 VOP 显示模块不支持对应能力，则需要依赖 RGA 处理。
