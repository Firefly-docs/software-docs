# Ubuntu/Debian 使用 netplan 配置网络

netplan 会读取 `/etc/netplan/` 下的 YAML 文件。请根据固件实际使用 NetworkManager 或 systemd-networkd 作为 renderer。下面示例使用 `networkd`；如果固件没有使用 networkd，可改为 `NetworkManager`。不要同时用 `nmcli` 和 netplan 管理同一个接口。

## 以太网 DHCP 示例

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
```

## 以太网静态地址示例

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

## Wi-Fi DHCP 示例

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

## 应用配置

```bash
sudo netplan generate
sudo netplan apply
```

如果正在通过网络远程操作，建议保留串口或本地显示连接，避免配置错误后无法继续访问设备。
