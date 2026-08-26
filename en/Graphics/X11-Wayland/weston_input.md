# Input Devices and Touchscreen Calibration

Weston requires at least one input device by default. If the product has no input device, disable this requirement in `weston.ini`:

```ini
[core]
require-input=false
```

In multi-display scenarios, bind a touchscreen to a specific output through udev rules:

```
ATTRS{name}=="goodix-ts", ENV{WL_OUTPUT}="HDMI-A-1"
```

Input binding can also be adjusted through the dynamic configuration file:

```bash
echo "output:HDMI-A-1:input=*" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:input=goodix-ts" > /tmp/.weston_drm.conf
```

Touchscreen calibration can be configured with `WESTON_TOUCH_CALIBRATION` or `LIBINPUT_CALIBRATION_MATRIX`. Use `weston-calibrator` or `weston-touch-calibrator` to get calibration parameters.
