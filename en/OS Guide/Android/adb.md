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
* [Open network ADB](network_adb.md)
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
    
