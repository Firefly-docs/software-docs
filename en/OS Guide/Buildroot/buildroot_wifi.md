# Buildroot WiFi Connection

## Modify the configuration file

### Method 1

Configuration via the qsetting QT application.

### Method 2

Modify the following files:

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
```

Add the following configuration items:

```bash
network={
ssid="WiFi-AP" // WiFi name
psk="12345678" // WiFi password
key_mgmt=WPA-PSK // encryption method
# key_mgmt=NONE // no encryption
}
```

Start the wpa_supplicant process:

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
```

## Temporary modification method

Modify the following files:

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
```

Start the wpa_supplicant process:

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
```

### Configure WiFi via wpa_cli

Common commands:

```bash
wpa_cli -i wlan0 scan // Search for nearby wifi networks
wpa_cli -i wlan0 scan_result // print search wifi network
wpa_cli -i wlan0 add_network // add a network connection
```

If the encryption method to connect is `WPA-PSK-CCMP+TKIP` / `WPA2-PSK-CCMP+TKIP` / `ESS` (wpa encryption), the wifi name is name, and the wifi password is: psk. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 psk '"psk"'
wpa_cli -i wlan0 set_network 0 key_mgmt WPA-PSK
wpa_cli -i wlan0 enable_network 0 //Enable WiFi
```

If the connection encryption method is `WEP` / `ESS` (wep encryption), the wifi name is name, and the wifi password is psk. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 set_network 0 wep_key0 '"psk"'
wpa_cli -i wlan0 enable_network 0
```

If the connection encryption method is `ESS` (no encryption), the wifi name is name. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 enable_network 0
```

Enable to save WIFI connection information:

```bash
wpa_cli -i wlan0 set update_config 1
```

Save WIFI connection information:

```bash
wpa_cli -i wlan0 save_config
```

Connect an existing connection:

```bash
wpa_cli -i wlan0 list_network // List all saved connections
wpa_cli -i wlan0 select_network 0 // connect to the first saved connection
wpa_cli -i wlan0 enable_network 0 // enable the first saved connection
```

Turn off WiFi:

```bash
ifconfig wlan0 down
```
