# Configure 4K UI

## Code modification method：
### Platform(RK3399/RK3568)
* Android7.1
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content:
    ```
    persist.sys.framebuffer.main=3840x2160@25
    ```
* Android10.0 and above
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.vendor.framebuffer.main=3840x2160@25
    ```

**NOTE**: The 4K UI occupies a large amount of system resources and supports a maximum of about 4K25Hz. You are not recommended to use the 4K UI. If you just want to play it 4K video or view 4K pictures, it can not need to configure 4K UI, the system's default video player and picture browser can support.

### Platform(RK3588)
* Android12.0 and above
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.vendor.framebuffer.main=3840x2160@60
    ```
**NOTE**:RK3588 has strong performance and supports 4K UI at 60Hz.

<!--
