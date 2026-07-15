# Firefly version
* View the firmware version 
    * In the Settings

        `Settings`-->`About tablet`-->`Firefly version`

        for example: 
        ```shell
        ROC-RK3399-PC+ HDMI+DP(Android)
        rk3399_roc_pc_plus/industry-71/V1.2.2008101849/98c828d3a1d2
        ```

# Open developer options 
* `Settings`-->`About tablet`-->`Build number`
* Click `Build number` at least 7 times 

# Use of ADB 
## Connect the device 
### USB way 
The host connects to the device via USB. For details on how to connect, please refer to the [WiKi Tutorial](https://wiki.t-firefly.com/en/) of the device.

* USB Type A interface 
    * Enable USB connection
		* Android7.1
		
        `Settings`-->`USB`-->`Connect to PC`
        
        * Android10/Android11
        
		`Settings`-->`Connected devices`-->`Connect to PC`
		
* USB Type C interface (enabled by default) 

### Network way 
The host connects to the device through the network (the host and the device are in the same LAN) 
* [Open network ADB](system_customization.md#open-network-adb-by-default)
* View the IP address of the device 

	`Settings`-->`About tablet`-->`Status`->`IP address`
* Connect to device via IP address 

    ```shell
    adb connect device_ip_address
    ```
    
## Common commands 
* Query device 

	```shell
    adb devices
    ```
* Stop adb server 

	```shell
    adb kill-server
    ```
    
* Install the app 

    ```shell
    adb install path_to_apk
    ```
    
* Copy files to device 

    ```shell
    adb push local remote
    ```
* Copy files from device 

    ```shell
    adb pull remote local
    ```
* Issue shell commands 

    ```shell
    adb [-d |-e | -s serial_number] shell shell_command
    ```
    
# Log 
## Last Log
In the `/sys/fs/pstore` directory of the device: 
* `console-ramoops-0`: last_log The kernel log started last time, but only the logs with higher priority than the default log level are saved 
* `pmsg-ramoops-0`: Last user space log, android log 

## Log is automatically saved 
### Way one (<font color=#FF0000>recommended use</font>) 
* [Open "Developer Options"](system_function.md#open-developer-options)
* Enable automatic log saving 

	* Android7.1
		
    `Settings`-->`Developer options`-->`Android bug collector`
    
    * Android10/Android11/Android12
		
    `Settings`-->`System`-->`Developer options`-->`Android LogSave`
    
* reboot device 
* There are log related files in the `/data/logs` directory of the device (**Android 10 and above: **`/data/vendor/logs`) 

    * `android`: system logcat 
    * `kernel`: kernel kmsg 

### Way two 
* [Open "Developer Options"](system_function.md#open-developer-options)
* Enable `Enable logging to save` 

	`Settings`-->`Developer options`-->`Enable logging to save`
* reboot device 
* There are log related files in the `/data/media/0/.LOGSAVE` directory of the device 
    * `xxxx_kmsg.log`: kernel kmsg 
    * `xxxx_logcat.log`: system logcat 

**NOTE:** Only supports Android 7.1 

### Way three 
* [Open "Developer Options"](system_function.md#open-developer-options)
* Enable `Android bug collector` 

	`Settings`-->`Developer options`-->`Android bug collector`
* reboot device 
* There are log related files in the `/data/media/0/rk_logs` directory of the device 
	* `COPY-COMPLETEB` means log has been copied 
	* `anr` means ANR file 
	* `bugreport.log` means the bugreport file 
	* `logs` means logcat, kmsg after each boot 
	* `pstore` means last_log 
	* `tombstones` means tombstones file 

**NOTE:** Only supports Android 7.1 


# Android system signature file 
<!--
## SDK source code 
### Android7.1
* build/target/product/security/platform.pk8
* build/target/product/security/platform.x509.pem

### Android10.0 or later
* build/make/target/product/security/platform.pk8
* build/make/target/product/security/platform.x509.pem
-->
## Cloud storage 

* [download](https://www.t-firefly.com/share/index/index/id/8b207377a05553a51cb8e523b7b98632.html)
<!--
* Android7.1 and above 
	* [Download here](http://www.t-firefly.com/share/index/index/id/cd2b5b6c71349b8dceddf5d251562de2.html)

<br>

* Android5.1 
	* [Download here](http://www.t-firefly.com/share/index/index/id/49ec72717582df08221e9e312a9612d2.html)
-->
# Timer switch 
* The device must have an RTC chip 
	* RK808
	* HYM8563
* Equipment is always powered 

You can directly use cat and echo to operate the interface under `/sys/class/rtc/rtc0`. 

## Set boot time 
For example, set it to boot after 120 seconds 
```shell
# Turn on regularly after 120 seconds 
echo +120 >  /sys/class/rtc/rtc0/wakealarm
# View boot time 
cat /sys/class/rtc/rtc0/wakealarm
# Shut down 
reboot -p
```

# Use of NPU 

| CPU | Board |
| ---- | ---- |
| RK3399 | [AIO-3399C(AI)](https://wiki.t-firefly.com/en/AIO-3399C/usage_npu.html) |
| RK3399Pro | [AIO-3399Pro-JD4](https://wiki.t-firefly.com/en/AIO-3399Pro-JD4/usage_npu.html) |
| RK3566 | [ROC-RK3566-PC](https://wiki.t-firefly.com/en/ROC-RK3566-PC/usage_npu.html) |
| RK3568 | [AIO-3568J](https://wiki.t-firefly.com/en/Core-3568J/usage_npu.html), [ROC-RK3568-PC](https://wiki.t-firefly.com/en/ROC-RK3568-PC/usage_npu.html) |
| RK3588 | [ITX-3588J](https://wiki.t-firefly.com/en/Core-3588J/usage_npu.html), [ROC-RK3588S-PC](https://wiki.t-firefly.com/en/ROC-RK3588S-PC/usage_npu.html) |

# Turn on the touch effect 
* [Open "Developer Options"](system_function.md#open-developer-options)
* Enable `Show taps` 
    * Android7.1<br>
    `Settings`-->`Developer options`-->`Show taps`
    * Android10/Android11/Android<br>
     `Settings`-->`System`-->`Developer options`-->`Show taps`
* Enable `Pointer location` 
    * Android7.1<br>
    `Settings`-->`Developer options`-->`Pointer location`
    * Android10/Android11/Android12<br>
    `Settings`-->`System`-->`Developer options`-->`Pointer location`

# Rockchip Encoder/Decoder
## MediaCodec
Android's [MediaCodec](https://developer.android.google.cn/reference/android/media/MediaCodec) calls the Rockchip Encoder/Decoder by default.
<br>
If you need native api, you can use MPP.

## MPP
Media Process Platform (MPP) provided by Rockchip is a general media processing software platform for Rockchip
chip series. For applications the MPP platform shields the complex lower-level processing related to chips. Its
purpose is to shield the differences between different chips and provide a unified media process interface (MPI)
to users.
* Documents: `SDK/RKDocs/common/MPP/`
* Source Code: [https://github.com/rockchip-linux/mpp](https://github.com/rockchip-linux/mpp)
