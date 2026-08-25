# 默认语言的修改
语言的支持列表可以查看`build/target/product/languages_full.mk`。

## 临时修改的方式
* 系统可读可写

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* 修改`/system/build.prop`文件中`ro.product.locale`的值(Android 10及以上为: `/vendor/build.prop`)
	* en-US：英文
	* zh-CN：中文
* 系统重启
	```shell
    adb shell reboot
    ```

## 代码修改的方式
* 在`build/target/product/full_base.mk`里修改`PRODUCT_LOCALES`的值
	* 中文
	
        ```
        PRODUCT_LOCALES := zh_CN
        ```
* 删除 out 目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
