# Modification of the default language 
The language support list can be viewed in `build/target/product/languages_full.mk`. 

## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify the value of `ro.product.locale` in the `/system/build.prop` file (Android10 and above: `/vendor/build.prop`) 
	* en-US: English 
	* zh-CN: Chinese 
* System restart 
	```shell
    adb shell reboot
    ```

## Code modification method 
* Modify the value of `PRODUCT_LOCALES` in `build/target/product/full_base.mk` 
	* Chinese 
	
        ```
        PRODUCT_LOCALES := zh_CN
        ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
