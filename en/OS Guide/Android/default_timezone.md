# Modification of the default time zone 
The time zone support list can be viewed in `frameworks/base/packages/SettingsLib/res/xml/timezones.xml`. 
## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify the value of `persist.sys.timezone` in the `/system/build.prop` file (Android10 and above: `/vendor/build.prop`) 
	* `Asia/Shanghai`：UTC+8
* System restart 

	```shell
    adb shell reboot
    ```

## Code modification method 
* Android7.1/Android10
    * In the file `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`, modify the value of `persist.sys.timezone` 
        * `Asia/Shanghai`：UTC+8
* Android11 and above
    * Add the following content in the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file: 
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.timezone=Asia/Shanghai
    ```
* Delete build.prop in the out directory 
    ```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

