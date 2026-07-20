# X11/Wayland Introduction

Linux graphics systems are usually built from a display protocol, display server or compositor, desktop environment, window manager, and GPU driver. On Rockchip platforms, the common graphics display stacks are X11/Xserver and Wayland/Weston.

## Default Support

| System | Default graphics stack | Description |
| --- | --- | --- |
| Ubuntu/Debian | X11 | Firefly RK platform Ubuntu/Debian desktop firmware uses X11 by default. A common stack is `LXDE/XFCE + Xserver + lightdm`. Wayland can run with a supported kernel, Mesa, and open-source Panfrost / Panthor GPU driver stack. For GPU driver details, see [GPU](../GPU/gpu.md). |
| Buildroot | Wayland | Firefly RK platform Buildroot graphics systems use Wayland by default. A common stack is `Weston + Wayland`, suitable for embedded full-screen UIs, multimedia display, and lightweight graphics applications. |

The actual behavior may vary by SoC, kernel version, GPU driver stack, and firmware configuration. Use the release notes and runtime environment of the actual firmware as the reference.

## X11/Xserver

X11 is the traditional display protocol widely used by Linux desktop systems. Xserver manages display devices, input devices, window rendering, and communication between client applications. Desktop environments, window managers, and graphics applications usually communicate with Xserver through the X11 protocol.

On Firefly Ubuntu/Debian firmware, X11 is commonly used for general desktop scenarios with a lightweight desktop environment and login manager:

```text
LXDE/XFCE + Xserver + lightdm
```

X11 is suitable for traditional desktops, window management, mouse and keyboard interaction, browsers, and common GUI applications. Xserver logs are usually stored at:

```bash
/var/log/Xorg.0.log
```

Show the Xserver version:

```bash
grep "X.Org X Server" /var/log/Xorg.0.log
```

Check whether the current session is X11:

```bash
echo $XDG_SESSION_TYPE
```

If the output is `x11`, the current session is running on X11.

### Rockchip X Server Enhancements

The X Server in Firefly Ubuntu/Debian firmware includes Rockchip platform adaptations and enhancements. The common configuration file is:

```bash
/etc/X11/xorg.conf.d/20-modesetting.conf
```

Common enhancements include:

- Primary GPU selection: match the `rockchip` DRM driver through `OutputClass` and set `PrimaryGPU` to avoid display issues caused by automatic GPU enumeration.
- Hardware acceleration: supports `glamor` and `exa` acceleration modes. `glamor` uses the GPU for rendering and composition; `exa` can use Rockchip RGA 2D hardware for rendering and composition.
- DRI configuration: configure the X11 direct rendering interface with the `DRI` option. A common value is `DRI` `2`.
- Tear-free display: configure the tear-free policy with `FlipFB`. Setting it to `always` can reduce screen tearing, but may reduce performance.
- Flip-rate limiting: use `MaxFlipRate` to limit the flip rate and drop frames to reduce the performance cost after tear-free display is enabled.
- EDID control: use `NoEDID` to disable display EDID, useful when the display reports incorrect EDID information.
- Gamma correction: use `UseGammaLUT` to enable the Gamma LUT and improve color display.
- Virtual screen size: use `VirtualSize` to set a virtual screen size and scale through VOP hardware.
- Display padding: use `Padding` to configure physical display edge padding.
- Screen rotation: set the rotation direction with `Rotate` in the `Monitor` section. Common values include `normal`, `left`, and `right`.

Example configuration:

```conf
Section "OutputClass"
    Identifier  "RockchipDRM"
    MatchDriver "rockchip"
    Option      "PrimaryGPU" "yes"
EndSection

Section "Device"
    Identifier  "Rockchip Graphics"
    Driver      "modesetting"
    Option      "AccelMethod" "glamor"
    Option      "DRI" "2"
    Option      "FlipFB" "always"
    Option      "NoEDID" "true"
    Option      "UseGammaLUT" "true"
EndSection

Section "Monitor"
    Identifier  "Default Monitor"
    Option      "Rotate" "normal"
EndSection
```

After changing the Xserver configuration, restart the graphical service or reboot the system for the changes to take effect.

### No Black Screen Implementation

During X11 desktop startup, a short black screen may appear between X service startup and the first desktop application frame. The duration depends on the desktop environment and application startup time. To keep the boot logo visible during this period, set `XSERVER_FREEZE_DISPLAY` during Xserver startup to temporarily freeze the display content, then unfreeze it after the desktop application is ready.

Add the following content before running `Xorg.wrap` in `/usr/bin/X`:

```bash
export XSERVER_FREEZE_DISPLAY=/.freeze_xserver
touch $XSERVER_FREEZE_DISPLAY
$(sleep 6; rm $XSERVER_FREEZE_DISPLAY)&
```

This example freezes the display for 6 seconds and then shows the desktop. Adjust the freeze time according to the actual product startup time.

You can also wait for a key desktop service before unfreezing the display, for example waiting for the panel service to start and finish drawing:

```bash
{
    export XSERVER_FREEZE_DISPLAY=/.freeze_xserver
    touch $XSERVER_FREEZE_DISPLAY
    while sleep .5; do
        pgrep panel && break
    done
    sleep 2
    rm $XSERVER_FREEZE_DISPLAY
}&
```

For production systems, use the target desktop application or key UI process as the ready condition. This avoids a fixed delay that is too short and still shows a black screen, or too long and slows visible startup.

## Wayland

Wayland is a newer Linux display protocol designed as an alternative to the traditional X11 architecture. In a Wayland stack, the compositor acts as the display server. Applications communicate with the compositor through the Wayland protocol, and the compositor handles window composition, input dispatch, and display output.

Wayland is suitable for modern graphics stacks, embedded UIs, low-latency display, and a more direct DRM/KMS display path. It usually works together with EGL, GBM, DRM/KMS, and the GPU driver.

Check whether the current session is Wayland:

```bash
echo $XDG_SESSION_TYPE
```

If the output is `wayland`, the current session is running on Wayland.

You can also check the Wayland display environment variable:

```bash
echo $WAYLAND_DISPLAY
```

## Weston

Weston is the reference compositor for the Wayland protocol and is commonly used as a Wayland display server implementation in embedded Linux. It can output directly through DRM/KMS and can be used as a desktop shell, full-screen shell, or kiosk-style graphics environment.

In embedded desktop systems built with Buildroot or Yocto, the common stack is:

```text
Weston + Wayland
```

Rockchip Buildroot SDK uses the Weston DRM backend as the default display service. Weston applications can provide basic demo functions such as a panel, background, Chromium browser, terminal, launchers, camera preview, multi-video display, GPU demos, and mouse demos. The system usually also includes Weston client test programs, for example:

```bash
weston-simple-egl
weston-simple-shm
weston-simple-touch
weston-screenshooter
weston-terminal
weston-touch-calibrator
```

Weston is suitable for:

- Embedded full-screen UIs.
- Multimedia playback and video display.
- Applications using Wayland backends, such as Qt, GTK, and GStreamer.
- Product systems that do not require a full traditional desktop environment.

Show Weston service status:

```bash
systemctl status weston
```

Show the Weston version:

```bash
weston --version
```

### Buildroot Weston Configuration

Common Weston configuration entry points in the Buildroot SDK are:

| Method | Path or example | Purpose |
| --- | --- | --- |
| Startup arguments | `/etc/init.d/S49weston` | Configure Weston startup arguments, such as `--tty=2`, `--idle-time=0`, and `--warm-up`. |
| weston.ini | `/etc/xdg/weston/weston.ini`, `/etc/xdg/weston/weston.ini.d/*.ini` | Configure common Weston sections such as core, shell, launcher, output, and libinput. |
| Environment variables | `/etc/profile.d/weston.sh` | Configure Rockchip Weston extension variables for multi-display, scaling, AFBC, touchscreen calibration, and similar features. |
| Dynamic configuration file | `/tmp/.weston_drm.conf` | Configure the DRM backend at runtime, such as display on/off, rotation, resolution, freeze, and multi-display position. Use `WESTON_DRM_CONFIG` to specify another path. |
| udev rules | `/lib/udev/rules.d/*.rules` | Bind input devices to displays or configure touchscreen calibration parameters. |

Weston uses the output/head name to identify display devices. Check the Weston startup log for names such as `DSI-1`, `HDMI-A-1`, and `eDP-1`:

```bash
weston --debug
```

### Common Display Configuration

Weston color format, rotation, resolution, and scaling can be configured in the `[core]` or `[output]` sections of `weston.ini`. In the Buildroot SDK, Weston uses `ARGB8888` by default. Low-performance platforms can switch to `rgb565` when appropriate:

```ini
[core]
gbm-format=rgb565
```

Configure a single display:

```ini
[output]
name=LVDS-1
gbm-format=rgb565
transform=rotate-90
mode=1280x800
scale=2
```

Runtime changes can also be made through the dynamic configuration file:

```bash
echo "output:all:rotate90" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:mode=800x600" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:size=1920x1080" > /tmp/.weston_drm.conf
```

To scale the UI while keeping the physical display mode unchanged, configure the following in `/etc/profile.d/weston.sh`:

```bash
export WESTON_DRM_VIRTUAL_SIZE=1024x768
```

If the hardware VOP display module does not support the required scaling operation, RGA is needed for processing.

### Freeze Display and Avoid Black Screen

During Weston startup, a short black screen may appear between the boot logo and the first UI frame. Rockchip Weston extensions can temporarily freeze the display content and unfreeze it after the UI is ready.

Method 1: use the custom `--warm-up` argument so Weston starts showing content after the UI starts:

```bash
/usr/bin/weston --warm-up&
```

Method 2: freeze the display with `WESTON_FREEZE_DISPLAY`:

```bash
export WESTON_FREEZE_DISPLAY=/tmp/.weston_freeze
touch /tmp/.weston_freeze
/usr/bin/weston&
sleep 1 && rm /tmp/.weston_freeze&
```

Method 3: freeze and unfreeze through the DRM dynamic configuration file:

```bash
echo "output:all:freeze" > /tmp/.weston_drm.conf
/usr/bin/weston&
sleep 1 && echo "output:all:unfreeze" > /tmp/.weston_drm.conf&
```

Adjust the freeze time according to the actual UI startup time. A delay that is too short may still show a black screen, while a delay that is too long slows visible startup.

### Display State and Multi-Display Management

Weston in the Buildroot SDK supports mirrored display, extended display, display position configuration, and hotplug. Multi-display policy can be configured in `/etc/profile.d/weston.sh`:

```bash
export WESTON_DRM_PRIMARY=HDMI-A-1
export WESTON_DRM_SINGLE_HEAD=1
export WESTON_DRM_MIRROR=1
export WESTON_DRM_KEEP_RATIO=1
export WESTON_DRM_HEAD_MODE=external-dual
export WESTON_DRM_HEAD_FALLBACK=1
export WESTON_OUTPUT_FLOW=horizontal
```

Display state and position can also be adjusted through the dynamic configuration file:

```bash
echo "output:DSI-1:off" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:freeze" > /tmp/.weston_drm.conf
echo "output:eDP-1:on" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:pos=100,200" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:primary" > /tmp/.weston_drm.conf
```

The DRM connector state can be forced through sysfs:

```bash
echo on > /sys/class/drm/card0-HDMI-A-1/status
```

Common `status` values are `on`, `off`, and `detect`. `detect` follows the hotplug state.

### Input Devices and Touchscreen Calibration

Weston requires at least one input device by default. If the product has no input device, disable this requirement in `weston.ini`:

```ini
[core]
require-input=false
```

In multi-display scenarios, bind a touchscreen to a specific output through udev rules:

```udev
ATTRS{name}=="goodix-ts", ENV{WL_OUTPUT}="HDMI-A-1"
```

Input binding can also be adjusted through the dynamic configuration file:

```bash
echo "output:HDMI-A-1:input=*" > /tmp/.weston_drm.conf
echo "output:HDMI-A-1:input=goodix-ts" > /tmp/.weston_drm.conf
```

Touchscreen calibration can be configured with `WESTON_TOUCH_CALIBRATION` or `LIBINPUT_CALIBRATION_MATRIX`. Use `weston-calibrator` or `weston-touch-calibrator` to get calibration parameters.

### Performance-Related Configuration

Weston in the SDK uses GPU rendering and composition acceleration by default. For platforms without a GPU or with insufficient GPU performance, enable the RGA and Pixman-related Buildroot options so Weston can use RGA for acceleration.

When the SoC supports AFBC, allow Weston to use GPU-based AFBC compressed display formats:

```bash
export WESTON_ALLOW_GBM_MODIFIERS=1
```

When UI performance or DDR bandwidth is insufficient, reduce the UI resolution:

```bash
echo "output:HDMI-A-1:down-scale=0.5" >> /tmp/.weston_drm.conf
```

## Selection Suggestions

- Use X11 first when a full desktop experience, browser, traditional windowed applications, or compatibility with older GUI programs is required.
- Use Wayland/Weston first for embedded full-screen UIs, multimedia display, low-latency display, or lightweight graphics systems.
- Ubuntu/Debian firmware uses X11 by default. Before validating Wayland, confirm that the GPU driver, Mesa, EGL, GBM, and application backend match.
- Buildroot firmware uses Wayland/Weston by default. For application development, prefer embedded display backends such as Wayland, DRM/KMS, or Qt EGLFS.
