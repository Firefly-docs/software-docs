# Several ways of setting built-in APP 
## Code modification method 
App folders can be added under the `vendor` directory 
### Not uninstallable 
Refer to `vendor/firefly/fireflyapi` 
* Contents in `Android.mk` 

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
    * `LOCAL_CERTIFICATE := platform` Meaning: APK signature uses system signature 
    * `LOCAL_PRIVILEGED_MODULE` If not set or set to false, the installation location is `/system/app` 


 * Contents in `fireflyapi.mk` 
 
     ```
     PRODUCT_PACKAGES += \
        	firefly_sdkapi_demo 

     PRODUCT_COPY_FILES += \
            vendor/firefly/fireflyapi/fireflyapi:system/bin/fireflyapi \
            vendor/firefly/fireflyapi/fireflyapi.jar:system/framework/fireflyapi.jar 
     ```
    
### Uninstallable 
<!-- 
Refer to `vendor/firefly/apps/FDeviceTest` 
* Contents in `Android.mk` 

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
    * `LOCAL_CERTIFICATE := PRESIGNED` Meaning: APK signature uses the original signature, that is, the third-party signature 
    * `LOCAL_PRIVILEGED_MODULE := true` Meaning: the installation location is `/system/priv-app` 
-->

* mkdir `device/rockchip/CPU_TYPE/PRODUCT_TYPE/preinstall_del`
* copy apk to `device/rockchip/CPU_TYPE/PRODUCT_TYPE/preinstall_del`
* build android

