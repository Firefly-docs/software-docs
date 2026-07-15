# Firefly version
* 固件版本的查看
    * 在 Settings 应用里

        `Settings`-->`About tablet`-->`Firefly version`

        比如：
        ```shell
        ROC-RK3399-PC+ HDMI+DP(Android)
        rk3399_roc_pc_plus/industry-71/V1.2.2008101849/98c828d3a1d2
        ```

# 开发者选项的打开
* `Settings`-->`About tablet`-->`Build number`
* 点击`Build number`至少7次

# ADB 的使用
## 连接设备
### USB 的方式
主机通过 USB 连接设备，具体如何连接，可参考设备的[WiKi教程](http://wiki.t-firefly.com/)。

* USB Type A 的接口
    * 使能 USB 连接
		* Android7.1
		
        `Settings`-->`USB`-->`Connect to PC`
        
        * Android10/Android11
        
		`Settings`-->`Connected devices`-->`Connect to PC`
		
* USB Type C 的接口（默认已经使能）

### 网络的方式
主机通过网络连接设备（主机和设备在同一个局域网内）
* [打开网络 ADB](system_customization.md#mo-ren-da-kai-wang-luo-adb)
* 查看设备的 IP 地址

	`Settings`-->`About tablet`-->`Status`->`IP address`
* 通过 IP 地址连接到设备

    ```shell
    adb connect device_ip_address
    ```
    
## 常用命令
* 查询设备

	```shell
    adb devices
    ```
* 停止 adb 服务器

	```shell
    adb kill-server
    ```
    
* 安装应用

    ```shell
    adb install path_to_apk
    ```
    
* 将文件复制到设备

    ```shell
    adb push local remote
    ```
* 从设备复制文件

    ```shell
    adb pull remote local
    ```
* 发出 shell 命令

    ```shell
    adb [-d |-e | -s serial_number] shell shell_command
    ```
    
# Log 日志
## Last Log
在设备的`/sys/fs/pstore`目录下：
* console-ramoops-0 : last_log 上次启动的 kernel log，但只保存里优先级比默认 log level 高的 log
* pmsg-ramoops-0 : 上次用户空间的log，android 的 log

## Log 自动保存
### 方式一（<font color=#FF0000>推荐使用</font>）
* [打开“开发者选项”](system_function.md#kai-fa-zhe-xuan-xiang-de-da-kai)
* 使能 Log 自动保存

	* Android7.1
		
    `Settings`-->`Developer options`-->`Android bug collector`
    
    * Android10/Android11/Android12
		
    `Settings`-->`System`-->`Developer options`-->`Android LogSave`
    
* 重启设备
* 在设备的`/data/logs`目录下有 log 相关的文件(**Android 10及以上为:**`/data/vendor/logs`)

    * `android`: 系统 logcat
    * `kernel`: 内核 kmsg

### 方式二
* [打开“开发者选项”](system_function.md#kai-fa-zhe-xuan-xiang-de-da-kai)
* 使能`Enable logging to save`

	`Settings`-->`Developer options`-->`Enable logging to save`
* 重启设备
* 在设备的`/data/media/0/.LOGSAVE`目录下有 log 相关的文件
    * xxxx_kmsg.log: 内核 kmsg
    * xxxx_logcat.log: 系统 logcat

**NOTE:** 仅支持Android 7.1

### 方式三
* [打开“开发者选项”](system_function.md#kai-fa-zhe-xuan-xiang-de-da-kai)
* 使能`Android bug collector`

	`Settings`-->`Developer options`-->`Android bug collector`
* 重启设备
* 在设备的`/data/media/0/rk_logs`目录下有 log 相关的文件
	* `COPY-COMPLETEB`表示 log 已经拷贝完成
	* `anr`表示ANR文件
	* `bugreport.log`表示 bugreport 文件
	* `logs`表示每次开机之后的 logcat，kmsg
	* `pstore`表示 last_log 
	* `tombstones`表示 tombstones 文件

**NOTE:** 仅支持Android 7.1


# Android 系统签名文件
<!--
## SDK 源码
### Android7.1
* build/target/product/security/platform.pk8
* build/target/product/security/platform.x509.pem

### Android10.0 or later
* build/make/target/product/security/platform.pk8
* build/make/target/product/security/platform.x509.pem
-->
## 网盘

<!--
* Android7.1及以上的版本
	* [此处下载](http://www.t-firefly.com/share/index/index/id/cd2b5b6c71349b8dceddf5d251562de2.html)

<br>

* Android5.1 的版本
	* [此处下载](http://www.t-firefly.com/share/index/index/id/49ec72717582df08221e9e312a9612d2.html)
-->

* [下载](https://www.t-firefly.com/share/index/index/id/8b207377a05553a51cb8e523b7b98632.html)

# 定时开关机
* 设备要有 RTC 芯片
	* RK808
	* HYM8563
* 设备常供电

可以直接使用 cat 和 echo 操作`/sys/class/rtc/rtc0`下面的接口。

## 设置开机时间
比如设置120秒后开机
```shell
#120秒后定时开机
echo +120 >  /sys/class/rtc/rtc0/wakealarm
# 查看开机时间
cat /sys/class/rtc/rtc0/wakealarm
#关机
reboot -p
```

# NPU 的使用

NPU使用详细参考对应机型

| 主控 | 机型 |
| ---- | ---- |
| RK3399 | [AIO-3399C(AI)](https://wiki.t-firefly.com/AIO-3399C/usage_npu.html) |
| RK3399Pro | [AIO-3399Pro-JD4](https://wiki.t-firefly.com/AIO-3399Pro-JD4/usage_npu.html) |
| RK3566 | [ROC-RK3566-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3566-PC/usage_npu.html) |
| RK3568 | [AIO-3568J](https://wiki.t-firefly.com/Core-3568J/usage_npu.html), [ROC-RK3568-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3568-PC/usage_npu.html) |
| RK3588 | [ITX-3588J](https://wiki.t-firefly.com/zh_CN/Core-3588J/usage_npu.html), [ROC-RK3588S-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3588S-PC/usage_npu.html) |


# 触摸效果的打开
* [打开“开发者选项”](system_function.md#kai-fa-zhe-xuan-xiang-de-da-kai)
* 使能`Show taps`
    * Android7.1<br>
    `Settings`-->`Developer options`-->`Show taps`
    * Android10/Android11/Android12<br>
     `Settings`-->`System`-->`Developer options`-->`Show taps`
* 使能`Pointer location`
    * Android7.1<br>
    `Settings`-->`Developer options`-->`Pointer location`
    * Android10/Android11/Android12<br>
    `Settings`-->`System`-->`Developer options`-->`Pointer location`

# Rockchip 编解码器
## MediaCodec
Android 的 [MediaCodec](https://developer.android.google.cn/reference/android/media/MediaCodec) 默认就会调用到 Rockchip 编解码器。
<br>
如果需要更底层的操作，可以使用 MPP。
## MPP
瑞芯微提供的媒体处理软件平台(Media Process Platform，简称 MPP)是适用于瑞芯微芯片系列的通用媒体处理软件平台。
该平台对应用软件屏蔽了芯片相关的复杂底层处理，其目的是为了屏蔽不同芯片的差异，为使用者提供统一的视频媒体处理接口(Media Process Interface，缩写 MPI)。
* 文档资料：`SDK/RKDocs/common/MPP/` 目录下
* MPP源码：[https://github.com/rockchip-linux/mpp](https://github.com/rockchip-linux/mpp)
