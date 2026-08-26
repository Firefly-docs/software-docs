# EtherCAT 主站启动

## 4. 主站配置与启动

### 4.1 选择 EtherCAT 网口

查看网口和 MAC 地址：

```bash
ip -br link
ip link show eth0
```

选择专用于 EtherCAT 的网口，并记录其永久 MAC 地址。不要使用会随机变化的 MAC 地址。

如果系统服务正在管理该网口，应先取消托管。不同发行版的配置方法不同，例如 NetworkManager 可使用：

```bash
nmcli device set eth0 managed no
```

### 4.2 加载通用 IgH 驱动

通用驱动示例：

```bash
sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_generic
```

将示例 MAC 地址替换为 EtherCAT 专用网口的实际地址。模块参数名称和配置文件路径可能随发行版打包方式变化，可使用以下命令确认：

```bash
modinfo ec_master
modinfo ec_generic
```

某些安装方式提供 `/etc/ethercat.conf` 或 `/etc/sysconfig/ethercat`，可在其中设置主站网口和驱动：

```text
MASTER0_DEVICE="62:36:B8:01:5B:59"
DEVICE_MODULES="generic"
```

配置文件格式应以安装包自带的服务脚本为准。

### 4.3 验证主站

```bash
ls -l /dev/EtherCAT*
sudo ethercat master
sudo ethercat slaves
dmesg | grep -i ethercat
```

正常情况下可以看到主站、链路状态和从站列表。如果没有从站：

1. 检查网线方向、从站电源和链路指示灯。
2. 确认 `main_devices` 使用了正确的 MAC 地址。
3. 使用 `ec_generic` 时确认标准网卡驱动工作正常，但普通网络服务没有在该网口收发 IP 流量。
4. 检查主站加载后绑定的实际设备。
5. 从单个从站开始测试，再逐步恢复完整拓扑。

### 4.4 停止主站

停止应用并确保输出进入安全状态后，再卸载驱动：

```bash
sudo modprobe -r ec_generic
sudo modprobe -r ec_master
```

使用平台专用驱动时，卸载顺序应与加载顺序相反。
