# Buildroot WiFi 连接

## 修改配置文件的方式

### 方式 1

通过 qsetting QT 应用进行配置。

### 方式 2

修改如下文件：

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
```

添加如下配置项：

```bash
network={
ssid="WiFi-AP"		// WiFi 名字
psk="12345678"		// WiFi 密码
key_mgmt=WPA-PSK	// 加密方式
# key_mgmt=NONE		// 不加密
}
```

启动 wpa_supplicant 进程：

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
```

## 临时修改的方式

修改如下文件：

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
```

启动 wpa_supplicant 进程：

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
```

### 通过 wpa_cli 配置 WiFi

常用命令：

```bash
wpa_cli -i wlan0 scan             // 搜索附近wifi网络
wpa_cli -i wlan0 scan_result      // 打印搜索wifi网络
wpa_cli -i wlan0 add_network      // 添加一个网络连接
```

如果要连接的加密方式是 `WPA-PSK-CCMP+TKIP`、`WPA2-PSK-CCMP+TKIP` 或 `ESS`（WPA 加密），WiFi 名称是 name，WiFi 密码是 psk，操作如下：

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 psk '"psk"'
wpa_cli -i wlan0 set_network 0 key_mgmt WPA-PSK
wpa_cli -i wlan0 enable_network 0    //使能WiFi
```

如果要连接的加密方式是 `WEP` 或 `ESS`（WEP 加密），WiFi 名称是 name，WiFi 密码是 psk，操作如下：

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 set_network 0 wep_key0 '"psk"'
wpa_cli -i wlan0 enable_network 0
```

如果要连接的加密方式是 `ESS`（无加密），WiFi 名称是 name，操作如下：

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 enable_network 0
```

使能保存 WiFi 连接信息：

```bash
wpa_cli -i wlan0 set update_config 1
```

保存 WiFi 连接信息：

```bash
wpa_cli -i wlan0 save_config
```

连接已有的连接：

```bash
wpa_cli -i wlan0 list_network        // 列举所有保存的连接
wpa_cli -i wlan0 select_network 0     // 连接第1个保存的连接
wpa_cli -i wlan0 enable_network 0      // 使能第1个保存的连接
```

关闭 WiFi：

```bash
ifconfig wlan0 down
```
