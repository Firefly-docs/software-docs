# Open network ADB by default 
## Temporary modification method 
### Way one 

* Android7.1
    ```
    adb shell setprop persist.internet.adb.enable 1
    ```
* Android10/Android11/Android12
    ```
    adb shell setprop persist.internet_adb_enable 1
    ```

### Way two
* [Open "Developer Options"](developer_options.md)
* Open `ADB over network` 

	`Settings`-->`Developer options`-->`ADB over network`
## Code modification method 
* Android7.1/Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

    ```
    persist.internet.adb.enable=1
    ```

* Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

    ```
    persist.internet_adb_enable=1
    ```

* Android11/Android12
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.internet_adb_enable=1
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
