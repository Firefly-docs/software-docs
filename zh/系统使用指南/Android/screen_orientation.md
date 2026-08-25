# 默认屏幕方向修改
## 临时修改的方式
* 系统可读可写

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* 修改或添加属性值

    * Android7.1

        * 在`/system/build.prop`文件中，修改或添加`ro.sf.hwrotation`的值

            * 0：横屏
            * 90：竖屏
            * 180：反向横屏
            * 270：反向竖屏

    * Android10及以上

        * 在`vendor/build.prop`文件中，修改或添加`ro.surface_flinger.primary_display_orientation`的值

            * ORIENTATION_0：横屏
            * ORIENTATION_90：竖屏
            * ORIENTATION_180：反向横屏
            * ORIENTATION_270：反向竖屏

        * Android14 还需在`vendor/build.prop`文件中，添加如下属性
            `debug.sf.ignore_hwc_physical_display_orientation=true`

* 系统重启

	```shell
    adb shell reboot
    ```

## 代码修改的方式 
* Android 7.1:

    在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，修改`ro.sf.hwrotation`的值

* Android 10及以上:

    在`device/rockchip/CPU_TYPE/BoardConfig.mk`文件里，修改`SF_PRIMARY_DISPLAY_ORIENTATION`的值

	* 0：横屏
	* 90：竖屏
	* 180：反向横屏
	* 270：反向竖屏
* 删除out目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

