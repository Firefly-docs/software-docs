# Ubuntu/Debian Network Configuration with netplan

Netplan reads YAML files from `/etc/netplan/`. Use either NetworkManager or systemd-networkd as the renderer according to the image. The examples below use `networkd`; change it to `NetworkManager` if the image does not use networkd. Do not manage the same interface with both `nmcli` and netplan at the same time.

## DHCP Ethernet example

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
```

## Static Ethernet example

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

## Wi-Fi DHCP example

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

## Apply the configuration

```bash
sudo netplan generate
sudo netplan apply
```

If remote access depends on the interface being changed, keep a serial console or local display available in case the network configuration is incorrect.
