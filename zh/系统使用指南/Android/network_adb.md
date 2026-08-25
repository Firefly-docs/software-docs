# 默认打开网络 ADB
## 临时修改的方式
### 方式一

* Android7.1
    ```
    adb shell setprop persist.internet.adb.enable 1
    ```

* Android10/Android11/Android12
    ```
    adb shell setprop persist.internet_adb_enable 1
    ```

### 方式二
* [打开“开发者选项”](developer_options.md)
* 打开 `ADB over network`

	`Settings`-->`Developer options`-->`ADB over network`
## 代码修改的方式
* Android7.1
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：

    ```
    persist.internet.adb.enable=1
    ```

* Android10
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：
    ```
    persist.internet_adb_enable=1
    ```

* Android11/Android12
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里，添加如下内容：

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.internet_adb_enable=1
    ```
* 删除 out 目录下的 build.prop

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
