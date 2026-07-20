# X11/Wayland 介绍

Linux 图形系统通常由显示协议、显示服务器或合成器、桌面环境、窗口管理器和 GPU 驱动共同组成。Rockchip 平台常见的图形显示方案主要包括 X11/Xserver 和 Wayland/Weston 两类。

## 默认支持情况

| 系统 | 默认图形方案 | 说明 |
| --- | --- | --- |
| Ubuntu/Debian | X11 | Firefly RK 平台 Ubuntu/Debian 桌面固件默认使用 X11，常见组合为 `LXDE/XFCE + Xserver + lightdm`。如需运行 Wayland，可在支持的内核、Mesa 和开源 GPU 驱动环境下使用 Panfrost / Panthor，相关 GPU 驱动说明可参考 [GPU](../GPU/gpu.md)。 |
| Buildroot | Wayland | Firefly RK 平台 Buildroot 图形系统默认使用 Wayland，常见组合为 `Weston + Wayland`，适合嵌入式全屏 UI、多媒体显示和轻量级图形应用。 |

不同芯片、内核版本、GPU 驱动栈和固件配置可能存在差异，请以实际固件发布说明和系统环境为准。

## X11/Xserver

X11 是传统 Linux 桌面系统中使用最广泛的显示协议，Xserver 负责管理显示设备、输入设备、窗口绘制和客户端应用之间的通信。桌面环境、窗口管理器和图形应用通常通过 X11 协议与 Xserver 交互。

在 Firefly Ubuntu/Debian 固件中，X11 常用于通用桌面场景，默认搭配轻量级桌面环境和登录管理器：

```text
LXDE/XFCE + Xserver + lightdm
```

X11 方案适合需要传统桌面、窗口管理、鼠标键盘交互、浏览器和常规 GUI 应用的场景。Xserver 的启动日志通常位于：

```bash
/var/log/Xorg.0.log
```

查看 Xserver 版本：

```bash
grep "X.Org X Server" /var/log/Xorg.0.log
```

查看当前会话是否为 X11：

```bash
echo $XDG_SESSION_TYPE
```

如果输出为 `x11`，表示当前会话运行在 X11 环境中。

### Rockchip X Server 增强功能

Firefly Ubuntu/Debian 固件中的 X Server 基于 Rockchip 平台做了适配和增强，常见配置文件为：

```bash
/etc/X11/xorg.conf.d/20-modesetting.conf
```

常见增强功能包括：

- 指定主 GPU：通过 `OutputClass` 匹配 `rockchip` DRM 驱动，并设置 `PrimaryGPU`，避免自动枚举显卡导致显示异常。
- 硬件加速：支持 `glamor` 和 `exa` 两种加速模式。`glamor` 使用 GPU 做渲染合成；`exa` 可使用 Rockchip RGA 2D 硬件做渲染合成。
- DRI 配置：可通过 `DRI` 选项配置 X11 直接渲染接口，常见配置为 `DRI` `2`。
- 防撕裂：可通过 `FlipFB` 设置防撕裂策略，例如设置为 `always` 后可减少屏幕撕裂，但可能带来性能损失。
- 刷新率限制：可通过 `MaxFlipRate` 限制 flip 频率并丢帧，用于降低开启防撕裂后的性能损失。
- EDID 控制：可通过 `NoEDID` 禁用显示器 EDID，适用于显示器 EDID 信息不准确的场景。
- Gamma 校正：可通过 `UseGammaLUT` 启用 Gamma LUT，改善颜色显示。
- 虚拟屏幕尺寸：可通过 `VirtualSize` 设置虚拟屏幕尺寸，由 VOP 硬件完成缩放。
- 屏幕边缘填充：可通过 `Padding` 设置物理显示边缘填充，用于调整屏幕边缘空白区域。
- 屏幕旋转：可在 `Monitor` 段通过 `Rotate` 设置旋转方向，常见值包括 `normal`、`left`、`right`。

示例配置片段：

```
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

修改 Xserver 配置后，通常需要重启图形服务或重启系统后生效。

### 无黑屏功能实现

在 X11 桌面启动过程中，X 服务启动完成到桌面应用开始显示之间可能会出现短暂黑屏，具体耗时取决于桌面环境和应用自身启动速度。如果希望这段时间继续保持启动 logo，可在 Xserver 启动阶段设置 `XSERVER_FREEZE_DISPLAY`，临时冻结显示内容，待桌面应用准备完成后再解除冻结。

可在 `/usr/bin/X` 中执行 `Xorg.wrap` 前添加如下内容：

```bash
export XSERVER_FREEZE_DISPLAY=/.freeze_xserver
touch $XSERVER_FREEZE_DISPLAY
$(sleep 6; rm $XSERVER_FREEZE_DISPLAY)&
```

上例会冻结显示 6 秒，然后恢复桌面显示。冻结时间可根据产品实际启动耗时调整。

也可以等待关键桌面服务启动后再解除冻结，例如等待状态栏服务启动并绘制完成：

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

实际产品中建议以目标桌面应用或关键 UI 进程作为判断条件，避免固定延时过短导致仍然黑屏，或固定延时过长影响开机显示速度。

## Wayland

Wayland 是新一代 Linux 显示协议，用于替代传统 X11 架构。Wayland 协议将显示服务器和合成器角色合并，应用通过 Wayland 协议直接与合成器通信，再由合成器完成窗口合成、输入分发和显示输出。

Wayland 架构更适合现代图形栈、嵌入式 UI、低延迟显示和更直接的 DRM/KMS 显示链路。它通常依赖 EGL、GBM、DRM/KMS 和 GPU 驱动配合工作。

查看当前会话是否为 Wayland：

```bash
echo $XDG_SESSION_TYPE
```

如果输出为 `wayland`，表示当前会话运行在 Wayland 环境中。

也可以查看 Wayland 显示环境变量：

```bash
echo $WAYLAND_DISPLAY
```

## Weston

Weston 是 Wayland 协议的参考合成器，也是嵌入式 Linux 中常用的 Wayland 显示服务器实现。它可以直接基于 DRM/KMS 输出显示，也可以作为桌面 shell、全屏 shell 或 kiosk 类图形环境使用。

在 Buildroot 或 Yocto 构建的嵌入式桌面系统中，常见组合为：

```text
Weston + Wayland
```

Rockchip Buildroot SDK 默认使用 Weston DRM 后端作为显示服务。Weston 应用可提供状态栏、背景、Chromium 浏览器、Terminal 终端、Launcher、摄像头预览、多路视频、GPU 和鼠标等基础演示功能。系统中也通常包含一些 Weston 客户端测试程序，例如：

```bash
weston-simple-egl
weston-simple-shm
weston-simple-touch
weston-screenshooter
weston-terminal
weston-touch-calibrator
```

Weston 适合以下场景：

- 嵌入式全屏 UI。
- 多媒体播放和视频显示。
- Qt、GTK、GStreamer 等支持 Wayland 后端的应用。
- 不需要完整传统桌面环境的产品系统。

查看 Weston 服务状态：

```bash
systemctl status weston
```

查看 Weston 版本：

```bash
weston --version
```

### Buildroot Weston 配置入口

Buildroot SDK 中 Weston 常见配置入口如下：

| 配置方式 | 路径或示例 | 用途 |
| --- | --- | --- |
| 启动参数 | `/etc/init.d/S49weston` | 配置 Weston 启动参数，例如 `--tty=2`、`--idle-time=0`、`--warm-up`。 |
| weston.ini | `/etc/xdg/weston/weston.ini`、`/etc/xdg/weston/weston.ini.d/*.ini` | 配置 core、shell、launcher、output、libinput 等 Weston 常规参数。 |
| 环境变量 | `/etc/profile.d/weston.sh` | 配置 Rockchip Weston 扩展环境变量，例如多屏、缩放、AFBC、触屏校准等。 |
| 动态配置文件 | `/tmp/.weston_drm.conf` | 运行时动态配置 DRM 后端，例如屏幕开关、旋转、分辨率、冻结、多屏位置等。可通过 `WESTON_DRM_CONFIG` 指定其他路径。 |
| udev rules | `/lib/udev/rules.d/*.rules` | 绑定输入设备和屏幕，或配置触屏校准参数。 |

Weston 使用 output/head name 区分屏幕设备，可通过 Weston 启动日志查看，例如 `DSI-1`、`HDMI-A-1`、`eDP-1`：

```bash
weston --debug
```

### 常用显示配置

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

### 冻结屏幕和无黑屏

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

### 屏幕状态和多屏管理

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

### 输入设备和触屏校准

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

### 性能相关配置

SDK 中 Weston 默认使用 GPU 做渲染合成加速。对于无 GPU 或 GPU 性能不足的平台，可在 Buildroot 配置中启用 RGA 和 Pixman 相关选项，让 Weston 使用 RGA 参与加速。

当芯片支持 AFBC 时，可允许 Weston 使用基于 GPU 的 AFBC 压缩格式显示：

```bash
export WESTON_ALLOW_GBM_MODIFIERS=1
```

当 UI 性能或 DDR 带宽不足时，可降低 UI 分辨率：

```bash
echo "output:HDMI-A-1:down-scale=0.5" >> /tmp/.weston_drm.conf
```

## 方案选择建议

- 需要完整桌面体验、浏览器、传统窗口应用或兼容旧 GUI 程序时，优先使用 X11。
- 需要嵌入式全屏 UI、多媒体显示、低延迟显示或轻量级图形系统时，优先使用 Wayland/Weston。
- Ubuntu/Debian 固件默认以 X11 为主。如果要验证 Wayland，应确认当前 GPU 驱动、Mesa、EGL、GBM 和应用后端是否匹配。
- Buildroot 固件默认以 Wayland/Weston 为主，应用开发时建议优先选择 Wayland、DRM/KMS 或 Qt EGLFS 等嵌入式显示后端。
