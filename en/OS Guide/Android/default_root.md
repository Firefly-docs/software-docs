# Default ROOT 
## Temporary modification method 
```shell
adb shell setprop persist.sys.root_access 3
```
## Code modification method 
* Android7.1/Android10.0
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

	```
    persist.sys.root_access=3
    ```
* Android11 and above
    * Add the following content in the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.root_access=3
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

