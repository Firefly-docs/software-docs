# 配置 USB 摄像头前置或者后置
默认是前置的
## 临时修改的方式
* 前置

    ```shell
    adb shell setprop persist.sys.uvc.facing front
    ```

* 后置

    ```shell
    adb shell setprop persist.sys.uvc.facing back
    ```

## 代码修改的方式
* Android7.1/Android10
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：

        * 前置
	
        ```
        persist.sys.uvc.facing=front
        ```
    
        * 后置
    
        ```
        persist.sys.uvc.facing=back
        ```
* Android11/Android12
     * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里，添加如下内容：

        * 前置

        ```
        PRODUCT_PROPERTY_OVERRIDES += persist.sys.uvc.facing=front
        ```

        * 后置

        ```
        PRODUCT_PROPERTY_OVERRIDES += persist.sys.uvc.facing=back
        ```
    
* 删除 out 目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

