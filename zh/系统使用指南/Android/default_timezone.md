# 默认时区的修改
时区的支持列表可以查看`frameworks/base/packages/SettingsLib/res/xml/timezones.xml`。
## 临时修改的方式
* 系统可读可写

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* 修改`/system/build.prop`文件中`persist.sys.timezone`的值(Android 10及以上为: `/vendor/build.prop`)
	* `Asia/Shanghai`：UTC+8
* 系统重启

	```shell
    adb shell reboot
    ```

## 代码修改的方式
* Android7.1/Android10
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，修改`persist.sys.timezone`的值
        * `Asia/Shanghai`：UTC+8
* Android11及以上
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里添加如下内容：
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.timezone=Asia/Shanghai
    ```
* 删除 out 目录下的 build.prop
    ```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

