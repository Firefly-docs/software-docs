# Default screen orientation modification 
## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify or add attribute values 

    * Android7.1

        * In the `/system/build.prop` file, modify or add the value of `ro.sf.hwrotation` 

            * 0：Horizontal screen 
            * 90：Portrait 
            * 180：Reverse landscape 
            * 270：Reverse portrait 

    * Android10 and above 

        * In the `vendor/build.prop` file, modify or add the value of `ro.surface_flinger.primary_display_orientation` 

            * ORIENTATION_0：Horizontal screen 
            * ORIENTATION_90：Portrait 
            * ORIENTATION_180：Reverse landscape
            * ORIENTATION_270：Reverse portrait 
        * Android14 also needs to add the following properties in the `vendor/build.prop` file
            `debug.sf.ignore_hwc_physical_display_orientation=true`

* System restart 

	```shell
    adb shell reboot
    ```

## Code modification method  
* Android 7.1:

    In the file `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`, modify the value of `ro.sf.hwrotation` 

* Android10 and above:

    In the file `device/rockchip/CPU_TYPE/BoardConfig.mk`, modify the value of `SF_PRIMARY_DISPLAY_ORIENTATION` 

	* 0：Horizontal screen 
	* 90：Portrait 
	* 180：Reverse landscape 
	* 270：Reverse portrait 
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

