# 冻结屏幕和无黑屏

启动 Weston 时，开机 logo 到 UI 显示之间可能出现短暂切换黑屏。可使用 Rockchip Weston 扩展短暂冻结屏幕内容，等 UI 启动后再显示。

方式一：使用定制 `--warm-up` 参数，让 Weston 在 UI 启动后开始显示：

```bash
/usr/bin/weston --warm-up&
```

方式二：通过 `WESTON_FREEZE_DISPLAY` 冻结显示：

```bash
export WESTON_FREEZE_DISPLAY=/tmp/.weston_freeze
touch /tmp/.weston_freeze
/usr/bin/weston&
sleep 1 && rm /tmp/.weston_freeze&
```

方式三：通过 DRM 动态配置冻结和解冻：

```bash
echo "output:all:freeze" > /tmp/.weston_drm.conf
/usr/bin/weston&
sleep 1 && echo "output:all:unfreeze" > /tmp/.weston_drm.conf&
```

冻结时间应根据产品实际 UI 启动耗时调整，避免时间过短仍出现黑屏，或时间过长影响开机显示速度。
