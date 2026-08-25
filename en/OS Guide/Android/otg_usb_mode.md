# The default setting of OTG USB3.0 is devices mode 
## Temporary modification method 
<!--
OTG_MODE: 0
HOST_MODE: 1
SLAVE_MODE: 2
-->
```shell
adb shell setprop persist.usb.mode 2
```
## Code modification method 
* Android7.1/Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

	```
    persist.usb.mode=2
    ```
* Android11/Android12
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.usb.mode=otg
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
