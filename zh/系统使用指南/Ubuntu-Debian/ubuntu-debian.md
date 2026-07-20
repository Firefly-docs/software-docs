# Ubuntu/Debian 使用说明

本文介绍 Firefly Ubuntu/Debian 固件上的常用操作。

## 登录用户和密码

默认登录账号：

- 用户：`firefly`
- 密码：`firefly`

Ubuntu Desktop 固件通常会自动登录到 `firefly` 桌面用户。需要管理员权限时使用 `sudo`：

```bash
sudo <command>
```

如需设置 root 密码，执行：

```bash
sudo passwd root
```

## 查看固件版本

Firefly 固件提供 `ffgo` 命令用于查看固件信息。向 Firefly 反馈问题时，建议附上 `ffgo version` 的输出。

```bash
ffgo
ffgo update
ffgo version
```

示例输出：

```text
OS:      Ubuntu 18.04.5 LTS
MODEL:   Firefly RK3566-ROC-PC HDMI(Linux)
FIREFLY: v2.04-1-g618089a
DATE:    20210316-1035
KERNEL:  Linux version 4.19.172 ...
```

## 性能模式

需要让各模块尽量定频到最高频时，可切换到 root 用户后执行：

```bash
sudo su
echo performance | tee $(find /sys/ -name '*governor')
```

命令运行时，部分节点可能会提示 `Permission denied`、`Invalid argument` 等错误，这是因为这些 governor 节点不支持用户态写入或不支持 `performance` 参数，不影响其他节点生效，可忽略。

性能模式会让支持的 CPU、devfreq 等模块切换到 `performance` governor，但如果系统温度过高，thermal 机制仍然会强制降频，以保护硬件。

## 关闭图形界面

部分使用场景不需要桌面环境，可将系统默认启动目标切换为 `multi-user.target`，下次启动后系统将进入命令行模式，不再自动启动图形界面：

```bash
sudo systemctl set-default multi-user.target
sudo reboot
```

如需恢复图形界面默认启动，执行：

```bash
sudo systemctl set-default graphical.target
sudo reboot
```

## 网络配置

配置网络前先确认实际网口名称：

```bash
ip link
nmcli device status
```

常见以太网接口名为 `eth0`、`enP...` 或 `end...`；常见 Wi-Fi 接口名为 `wlan0` 或 `wlP...`。

### 使用 nmcli 配网

查看设备和连接：

```bash
nmcli device status
nmcli connection show
```

请将 `"Wired connection 1"` 替换成实际连接名。如果没有以太网连接，可先创建：

```bash
sudo nmcli connection add con-name "eth0-static" ifname eth0 type ethernet
```

以太网使用 DHCP：

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method auto
sudo nmcli connection up "Wired connection 1"
```

以太网设置静态地址：

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.100/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "Wired connection 1"
```

Wi-Fi 使用 DHCP 连接：

```bash
sudo nmcli radio wifi on
nmcli device wifi list
sudo nmcli device wifi connect "SSID" password "PASSWORD"
```

给已有 Wi-Fi 连接设置静态地址：

```bash
sudo nmcli connection modify "SSID" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.101/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "SSID"
```

### 使用 netplan 配网

netplan 会读取 `/etc/netplan/` 下的 YAML 文件。请根据固件实际使用 NetworkManager 或 systemd-networkd 作为 renderer。下面示例使用 `networkd`；如果固件没有使用 networkd，可改为 `NetworkManager`。不要同时用 `nmcli` 和 netplan 管理同一个接口。

以太网 DHCP 示例：

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
```

以太网静态地址示例：

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
      addresses:
        - 192.168.1.100/24
      nameservers:
        addresses:
          - 8.8.8.8
          - 114.114.114.114
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
```

Wi-Fi DHCP 示例：

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: true
      access-points:
        "SSID":
          password: "PASSWORD"
```

应用配置：

```bash
sudo netplan generate
sudo netplan apply
```

如果正在通过网络远程操作，建议保留串口或本地显示连接，避免配置错误后无法继续访问设备。

## 配置应用开机自启动

### 桌面环境自启动

可将 `.desktop` 文件放到用户自启动目录；系统会在该用户登录桌面后启动程序：

```bash
mkdir -p ~/.config/autostart
vim ~/.config/autostart/myapp.desktop
```

写入以下内容，请将 `Exec` 改为实际程序路径：

```
[Desktop Entry]
Type=Application
Name=My App
Exec=/home/firefly/myapp/myapp
Terminal=false
```

如需所有桌面用户都自动启动同一个程序，可将 `.desktop` 文件放到系统目录：

```bash
sudo cp ~/.config/autostart/myapp.desktop /etc/xdg/autostart/
```

### systemd 服务自启动

需要作为后台服务运行的应用，建议创建 systemd 服务。

创建 `/etc/systemd/system/myapp.service`：

```ini
[Unit]
Description=My Application
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=firefly
WorkingDirectory=/home/firefly/myapp
ExecStart=/home/firefly/myapp/myapp
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

启用并启动服务：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
sudo systemctl status myapp.service
```

查看日志：

```bash
journalctl -u myapp.service -f
```

如果服务启动的是图形程序，需要补充显示环境和权限，例如：

```ini
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/firefly/.Xauthority
```

## 恢复出厂设置

内置 `recovery` 工具的 Firefly Ubuntu/Debian 固件支持恢复出厂设置。这里的出厂设置表示恢复为设备最后一次升级固件之后的初始状态。执行前请备份重要数据。

查看 recovery 用法：

```bash
recovery
```

恢复工具包含以下 reset 选项：

```text
factory | reset:
        reset to factory
```

执行恢复出厂设置：

```bash
sudo recovery reset
```
