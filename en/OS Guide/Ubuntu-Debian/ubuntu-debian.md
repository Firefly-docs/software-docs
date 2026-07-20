# Ubuntu/Debian Usage

This document describes common operations on Firefly Ubuntu/Debian firmware.

## Login User and Password

The default login account is:

- User: `firefly`
- Password: `firefly`

On Ubuntu Desktop firmware, the system usually logs in to the `firefly` desktop user automatically. Use `sudo` when administrator permission is required:

```bash
sudo <command>
```

If you need to set a root password, run:

```bash
sudo passwd root
```

## Show Firmware Version

Firefly firmware provides the `ffgo` command to show firmware information. When reporting issues, include the output of `ffgo version`.

```bash
ffgo
ffgo update
ffgo version
```

Example output:

```text
OS:      Ubuntu 18.04.5 LTS
MODEL:   Firefly RK3566-ROC-PC HDMI(Linux)
FIREFLY: v2.04-1-g618089a
DATE:    20210316-1035
KERNEL:  Linux version 4.19.172 ...
```

## Performance Mode

To keep each module running at its highest frequency as much as possible, switch to the root user and run:

```bash
sudo su
echo performance | tee $(find /sys/ -name '*governor')
```

Some nodes may report errors such as `Permission denied` or `Invalid argument`. This means those governor nodes do not support user-space writes or do not support the `performance` value. The errors do not affect other nodes that were updated and can be ignored.

Performance mode switches supported CPU, devfreq, and similar modules to the `performance` governor, but if the system temperature becomes too high, the thermal mechanism will still force frequency throttling to protect the hardware.

## Show SoC Module Temperatures

SoC module temperatures can be read from thermal nodes. First, show the type of each thermal zone:

```bash
cat /sys/class/thermal/thermal_zone*/type
```

Example output:

```text
soc-thermal
bigcore0-thermal
bigcore1-thermal
littlecore-thermal
center-thermal
gpu-thermal
npu-thermal
```

Then show the temperature of the corresponding thermal zones:

```bash
cat /sys/class/thermal/thermal_zone*/temp
```

Example output:

```text
38846
38846
38846
38846
38846
38846
38846
```

The unit is millidegrees Celsius. Divide the value by 1000 to get degrees Celsius. For example, `38846` means about `38.846°C`. The number and names of thermal zones may vary between SoCs and firmware versions. Use the actual node output as the reference.

## Disable the Graphical Interface

Some use cases do not require a desktop environment. You can change the default boot target to `multi-user.target`. After the next reboot, the system will boot to command-line mode and will not start the graphical interface automatically:

```bash
sudo systemctl set-default multi-user.target
sudo reboot
```

To restore graphical startup by default, run:

```bash
sudo systemctl set-default graphical.target
sudo reboot
```

## Network Configuration

Confirm the actual interface name before editing network settings:

```bash
ip link
nmcli device status
```

Common Ethernet interface names are `eth0`, `enP...`, or `end...`; common Wi-Fi interface names are `wlan0` or `wlP...`.

### Configure with nmcli

Show devices and connections:

```bash
nmcli device status
nmcli connection show
```

Replace `"Wired connection 1"` with the actual connection name. If no Ethernet connection exists, create one first:

```bash
sudo nmcli connection add con-name "eth0-static" ifname eth0 type ethernet
```

Use DHCP on Ethernet:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method auto
sudo nmcli connection up "Wired connection 1"
```

Set a static Ethernet address:

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.100/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "Wired connection 1"
```

Connect to Wi-Fi with DHCP:

```bash
sudo nmcli radio wifi on
nmcli device wifi list
sudo nmcli device wifi connect "SSID" password "PASSWORD"
```

Set a static address for an existing Wi-Fi connection:

```bash
sudo nmcli connection modify "SSID" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.101/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 114.114.114.114"
sudo nmcli connection up "SSID"
```

### Configure with netplan

Netplan reads YAML files from `/etc/netplan/`. Use either NetworkManager or systemd-networkd as the renderer according to the image. The examples below use `networkd`; change it to `NetworkManager` if the image does not use networkd. Do not manage the same interface with both `nmcli` and netplan at the same time.

DHCP Ethernet example:

```yaml
# /etc/netplan/01-firefly.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true
```

Static Ethernet example:

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

Wi-Fi DHCP example:

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

Apply the configuration:

```bash
sudo netplan generate
sudo netplan apply
```

If remote access depends on the interface being changed, keep a serial console or local display available in case the network configuration is incorrect.

## Configure Application Autostart

### Desktop Autostart

Place the `.desktop` file in the user autostart directory. The system starts the program after that user logs in to the desktop:

```bash
mkdir -p ~/.config/autostart
vim ~/.config/autostart/myapp.desktop
```

Write the following content and change `Exec` to the actual application path:

```
[Desktop Entry]
Type=Application
Name=My App
Exec=/home/firefly/myapp/myapp
Terminal=false
```

To start the same program for all desktop users, place the `.desktop` file in the system directory:

```bash
sudo cp ~/.config/autostart/myapp.desktop /etc/xdg/autostart/
```

### systemd Service Autostart

For applications that should run as a background service, create a systemd unit.

Create `/etc/systemd/system/myapp.service`:

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

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
sudo systemctl status myapp.service
```

View logs:

```bash
journalctl -u myapp.service -f
```

If the service starts a GUI program, set the required display environment and permissions, for example:

```ini
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/firefly/.Xauthority
```

## Restore Factory Settings

Firefly Ubuntu/Debian firmware that includes the `recovery` tool supports restoring factory settings. This means restoring the device to the initial state after the last firmware upgrade. Back up important data before running the command.

Show recovery usage:

```bash
recovery
```

The recovery tool includes the following reset option:

```text
factory | reset:
        reset to factory
```

Restore factory settings:

```bash
sudo recovery reset
```
