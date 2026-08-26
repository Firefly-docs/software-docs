# Ubuntu/Debian 网络配置

配置网络前先确认实际网口名称：

```bash
ip link
nmcli device status
```

常见以太网接口名为 `eth0`、`enP...` 或 `end...`；常见 Wi-Fi 接口名为 `wlan0` 或 `wlP...`。

请选择一种网络管理方式：

- [使用 nmcli 配网](ubuntu_network_nmcli.md)
- [使用 netplan 配网](ubuntu_network_netplan.md)
