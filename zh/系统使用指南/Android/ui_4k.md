# 配置4K UI

## 代码修改方式：
### 平台(RK3399/RK356x)
* Android7.1
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`文件里，添加如下内容：
    ```
    persist.sys.framebuffer.main=3840x2160@25
    ```
* Android10.0及以上
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里，添加如下内容：
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.vendor.framebuffer.main=3840x2160@25
    ```

**NOTE**: 配置 4K UI 占用系统资源较多，最高只能支持到 4K25Hz 左右，不推荐使用 4K UI。如果只是想要播放
4K 视频或是查看 4K 图片，那可以不需要配置 4K UI，系统默认的视频播放器和图片浏览器可以支持。

### 平台(RK3588)
* Android12.0及以上
    * 在`device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk`文件里，添加如下内容：
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.vendor.framebuffer.main=3840x2160@60
    ```
**NOTE**:RK3588 性能强劲可以支持4K UI 60Hz。

<!--
