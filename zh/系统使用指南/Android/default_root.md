# 默认 ROOT
## 临时修改的方式
```shell
adb shell setprop persist.sys.root_access 3
```
## 代码修改的方式 
* Android7.1/Android10.0
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：

	```
    persist.sys.root_access=3
    ```
* Android11及以上
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里添加如下内容：

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.root_access=3
    ```
* 删除 out 目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

