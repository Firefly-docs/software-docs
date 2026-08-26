# 屏幕状态和多屏管理

Buildroot SDK 的 Weston 支持多屏镜像同显、多屏异显、屏幕位置配置和热拔插。可在 `/etc/profile.d/weston.sh` 中配置多屏策略：

```bash
export WESTON_DRM_PRIMARY=HDMI-A-1
export WESTON_DRM_SINGLE_HEAD=1
export WESTON_DRM_MIRROR=1
export WESTON_DRM_KEEP_RATIO=1
export WESTON_DRM_HEAD_MODE=external-dual
export WESTON_DRM_HEAD_FALLBACK=1
export WESTON_OUTPUT_FLOW=horizontal
```

也可以通过动态配置文件调整屏幕状态和位置：

```bash
echo "output:DSI-1:off" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:freeze" > /tmp/.weston_drm.conf
echo "output:eDP-1:on" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:pos=100,200" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:primary" > /tmp/.weston_drm.conf
```

强制 DRM connector 状态可通过 sysfs 设置：

```bash
echo on > /sys/class/drm/card0-HDMI-A-1/status
```

`status` 常见值包括 `on`、`off`、`detect`，其中 `detect` 表示按热拔插状态检测。
