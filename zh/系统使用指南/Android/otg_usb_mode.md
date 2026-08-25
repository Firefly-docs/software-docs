# 默认设置 OTG USB3.0 为 devices 模式
## 临时修改的方式
<!--
OTG_MODE: 0
HOST_MODE: 1
SLAVE_MODE: 2
-->
```shell
adb shell setprop persist.usb.mode 2
```
## 代码修改的方式
* Android7.1/Android10
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：

	```
    persist.usb.mode=2
    ```
* Android11/Android12
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里，添加如下内容：

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.usb.mode=otg
    ```
* 删除 out 目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
