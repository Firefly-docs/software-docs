# 内置 APP 的几种方式
## 代码修改的方式
可在`vendor`目录下新增APP的文件夹
### 不可卸载
可参考`vendor/firefly/fireflyapi`
* `Android.mk`里的内容

    ```
    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)
    LOCAL_MODULE := firefly_sdkapi_demo
    LOCAL_MODULE_CLASS := APPS
    LOCAL_MODULE_TAGS := optional
    LOCAL_BUILT_MODULE_STEM := package.apk
    LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
    #LOCAL_PRIVILEGED_MODULE :=
    LOCAL_CERTIFICATE := platform
    #LOCAL_OVERRIDES_PACKAGES := 
    LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
    #LOCAL_REQUIRED_MODULES :=
    #LOCAL_PREBUILT_JNI_LIBS :=
    include $(BUILD_PREBUILT)
    ```
    * `LOCAL_CERTIFICATE := platform`含义：APK签名使用系统签名
    * `LOCAL_PRIVILEGED_MODULE`如果不设置或者设置为false，安装位置为`/system/app`


 * `fireflyapi.mk`里的内容
 
     ```
     PRODUCT_PACKAGES += \
        	firefly_sdkapi_demo 

     PRODUCT_COPY_FILES += \
            vendor/firefly/fireflyapi/fireflyapi:system/bin/fireflyapi \
            vendor/firefly/fireflyapi/fireflyapi.jar:system/framework/fireflyapi.jar 
     ```
    
### 可卸载
<!--
可参考`vendor/firefly/apps/FDeviceTest`
* `Android.mk`里的内容

	```
    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)
    LOCAL_MODULE := FDeviceTest
    LOCAL_MODULE_CLASS := APPS
    LOCAL_MODULE_TAGS := optional
    LOCAL_BUILT_MODULE_STEM := package.apk
    LOCAL_DEX_PREOPT := nostripping
    LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
    LOCAL_PRIVILEGED_MODULE := true
    LOCAL_CERTIFICATE := PRESIGNED
    LOCAL_OVERRIDES_PACKAGES := DeviceTest
    LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
    LOCAL_MULTILIB := 32
    #LOCAL_REQUIRED_MODULES :=
    JNI_LIBS :=
    $(foreach FILE,$(shell find $(LOCAL_PATH)/lib/ -name *.so), $(eval JNI_LIBS += $(FILE)))
    LOCAL_PREBUILT_JNI_LIBS := $(subst $(LOCAL_PATH),,$(JNI_LIBS))

    include $(BUILD_PREBUILT)
    ```
    * `LOCAL_CERTIFICATE := PRESIGNED`含义：APK签名使用原来签名，即第三方签名
    * `LOCAL_PRIVILEGED_MODULE := true`含义：安装位置为`/system/priv-app`
-->

* 在 `device/rockchip/CPU_TYPE/PRODUCT_TYPE/` 目录下新建一个 `preinstall_del` 目录
* 把 apk 拷贝到 `device/rockchip/CPU_TYPE/PRODUCT_TYPE/preinstall_del` 目录下
* 编译 android

