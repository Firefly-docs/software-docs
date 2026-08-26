# 输入设备和触屏校准

Weston 默认需要至少一个输入设备。如果产品没有输入设备，可在 `weston.ini` 中关闭该要求：

```ini
[core]
require-input=false
```

多屏场景下，可通过 udev rules 将触摸屏绑定到指定输出：

```
ATTRS{name}=="goodix-ts", ENV{WL_OUTPUT}="HDMI-A-1"
```

也可以通过动态配置文件调整输入设备绑定：

```bash
echo "output:HDMI-A-1:input=*" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:input=goodix-ts" > /tmp/.weston_drm.conf
```

触屏校准可通过 `WESTON_TOUCH_CALIBRATION` 或 `LIBINPUT_CALIBRATION_MATRIX` 配置，校准参数可使用 `weston-calibrator` 或 `weston-touch-calibrator` 获取。
