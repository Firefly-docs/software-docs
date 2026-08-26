# Buildroot Weston 配置入口

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
