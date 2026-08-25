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
* [打开网络 ADB](network_adb.md)
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
    
