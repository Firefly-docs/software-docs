# Ubuntu/Debian Network Configuration

Confirm the actual interface name before editing network settings:

```bash
ip link
nmcli device status
```

Common Ethernet interface names are `eth0`, `enP...`, or `end...`; common Wi-Fi interface names are `wlan0` or `wlP...`.

Choose one network management method:

- [Configure with nmcli](ubuntu_network_nmcli.md)
- [Configure with netplan](ubuntu_network_netplan.md)
