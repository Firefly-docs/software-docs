# Configure USB camera front or rear
The default is front.
## Temporary modification method 
* Front 

    ```shell
    adb shell setprop persist.sys.uvc.facing front
    ```

* Back

    ```shell
    adb shell setprop persist.sys.uvc.facing back
    ```

## Code modification method 
* Android7.1/Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

        * Front
	
        ```
        persist.sys.uvc.facing=front
        ```
    
        * Back
    
        ```
        persist.sys.uvc.facing=back
        ```
* Android11/Android12
     * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 

        * Front

        ```
        PRODUCT_PROPERTY_OVERRIDES += persist.sys.uvc.facing=front
        ```

        * Back

        ```
        PRODUCT_PROPERTY_OVERRIDES += persist.sys.uvc.facing=back
        ```
    
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

