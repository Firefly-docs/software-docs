# Ubuntu/Debian Network Configuration with nmcli

## Show devices and connections

```bash
nmcli device status
nmcli connection show
```

Replace `"Wired connection 1"` with the actual connection name. If no Ethernet connection exists, create one first:

```bash
sudo nmcli connection add con-name "eth0-static" ifname eth0 type ethernet
```

## Use DHCP on Ethernet

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method auto
sudo nmcli connection up "Wired connection 1"
```

## Set a static Ethernet address

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.100/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "Wired connection 1"
```

## Connect to Wi-Fi with DHCP

```bash
sudo nmcli radio wifi on
nmcli device wifi list
sudo nmcli device wifi connect "SSID" password "PASSWORD"
```

## Set a static address for an existing Wi-Fi connection

```bash
sudo nmcli connection modify "SSID" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.101/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "SSID"
```
