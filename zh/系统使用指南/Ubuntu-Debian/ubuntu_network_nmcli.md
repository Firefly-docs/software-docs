# Ubuntu/Debian 使用 nmcli 配置网络

## 查看设备和连接

```bash
nmcli device status
nmcli connection show
```

请将 `"Wired connection 1"` 替换成实际连接名。如果没有以太网连接，可先创建：

```bash
sudo nmcli connection add con-name "eth0-static" ifname eth0 type ethernet
```

## 以太网使用 DHCP

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method auto
sudo nmcli connection up "Wired connection 1"
```

## 以太网设置静态地址

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.100/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "Wired connection 1"
```

## Wi-Fi 使用 DHCP 连接

```bash
sudo nmcli radio wifi on
nmcli device wifi list
sudo nmcli device wifi connect "SSID" password "PASSWORD"
```

## 给已有 Wi-Fi 连接设置静态地址

```bash
sudo nmcli connection modify "SSID" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.101/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "SSID"
```
