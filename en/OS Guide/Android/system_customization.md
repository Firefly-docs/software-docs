
# Overview
## Scope of application 
* RK3399 Android7.1 Industry
* RK3399 Android10
* RK356X Android11
* RK3588 Android12

For other Android versions, it is available for reference.
<br>
<br>

## Modification method 
For Android system customization, there are generally **Temporary modification methods** and **Code modification methods**.
* Temporary modification method：Generally, the device can be operated through the debug serial port or ADB.
* Code modification method：After modifying the code, you need to perform <font color=#FF0000> SDK compilation</font> and <font color=#FF0000> firmware flashing</font>. 

Among them, *SDK compilation* or *firmware flashing* [refer to WiKi tutorial](https://wiki.t-firefly.com/en/)。

## Glossary
* `CPU_TYPE`：Indicates the main control chip, such as rk3288, rk3399, rk3399pro, rk356x, rk3588
* `PRODUCT_TYPE`：Indicates the product type, such as rk3399_firefly, rk3399_firefly_aio, rk3399_firefly_aioc_ai 

## Custom description 
For the customized content of the system, there will be some differences in Android12, Android11, Android10.0 and Android7.1 Industry. This part of the content will be distinguished, and the undifferentiated content indicates compatibility. 

# Boot logo animation modification 
## The first stage 
### Code modification method 
* Prepare the logo 
    * Make a new logo.bmp 
    	* The image attributes are the same as the default logo.bmp, otherwise an upside-down exception will occur 
    	* Both width and height are even numbers 
* Replace logo 
    * Replace `kernel/logo.bmp` 

## Second stage 
### Temporary modification method 
* Prepare bootanimation.zip 
	* Prepare png file 
		* Both width and height are even numbers 
	* Prepare desc.txt file, such as 
	
		```shell
        800 1280 30
		p 0 0 part0
        ```
        * `800 1280 30`Meaning: the first two numbers represent the pixel width and height of the picture, 30 represents the number of frames, which is the number of pictures played in 1 second 
        * `p 0 0 part0`Meaning: p stands for flag bit, 0 stands for infinite loop, 0 stands for the interval time between stages is 0, part0 stands for the corresponding folder 
        * desc.txt text format: Unix+UTF-8 
	* Make bootanimation.zip 
		* Compressed into a zip file in **storage method** 
		
* Push bootanimation.zip

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    adb push bootanimation.zip system/media/bootanimation.zip
    adb shell reboot
    ```

### Code modification method 
* Prepare bootanimation.zip 
	* Put the prepared `bootanimation.zip` into the `device/rockchip/CPU_TYPE/` directory 
* In the `device/rockchip/CPU_TYPE/device.mk` file, add the following content: 

    ```
    PRODUCT_COPY_FILES += \
           device/rockchip/CPU_TYPE/bootanimation.zip:/system/media/bootanimation.zip
    ```

# Default screen orientation modification 
## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify or add attribute values 

    * Android7.1

        * In the `/system/build.prop` file, modify or add the value of `ro.sf.hwrotation` 

            * 0：Horizontal screen 
            * 90：Portrait 
            * 180：Reverse landscape 
            * 270：Reverse portrait 

    * Android10 and above 

        * In the `vendor/build.prop` file, modify or add the value of `ro.surface_flinger.primary_display_orientation` 

            * ORIENTATION_0：Horizontal screen 
            * ORIENTATION_90：Portrait 
            * ORIENTATION_180：Reverse landscape
            * ORIENTATION_270：Reverse portrait 
        * Android14 also needs to add the following properties in the `vendor/build.prop` file
            `debug.sf.ignore_hwc_physical_display_orientation=true`

* System restart 

	```shell
    adb shell reboot
    ```

## Code modification method  
* Android 7.1:

    In the file `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`, modify the value of `ro.sf.hwrotation` 

* Android10 and above:

    In the file `device/rockchip/CPU_TYPE/BoardConfig.mk`, modify the value of `SF_PRIMARY_DISPLAY_ORIENTATION` 

	* 0：Horizontal screen 
	* 90：Portrait 
	* 180：Reverse landscape 
	* 270：Reverse portrait 
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

# Hide the status bar by default 
## Temporary modification method 
In the Settings, just enable the option. 

`Settings`-->`Display`-->`Always hide the status bar`

### Code modification method 
* The revised content is as follows   

    ```
    diff --git a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    index ba87da7ec4..e32c9699f9 100644
    --- a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    +++ b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    @@ -12,7 +12,7 @@
         <!-- should show the hidebar button default -->
         <bool name="def_hidebar_button_show">true</bool>
         <!-- always hide statusbar -->
    -    <bool name="def_always_hide_bar">false</bool>
    +    <bool name="def_always_hide_bar">true</bool>

         <!-- will set DEFAULT_LAUNCHER in frist boot -->
         <string name="def_default_launcher" translatable="false"></string>
    ```

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

# Setting of built-in default input method 
## Code modification method 
* Get the input method package name 

	```shell
    adb shell settings get secure enabled_input_methods
    com.android.inputmethod.latin/.LatinIME:com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME:com.google.android.inputmethod.pinyin/.PinyinIME
    adb shell settings get secure default_input_method
    com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME
    ```

* Modify the code as follows 

    ```
    diff --git a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    index ba87da7ec4..7ed79b4c15 100644
    --- a/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    +++ b/frameworks/base/packages/SettingsProvider/res/values/custom_defaults.xml
    @@ -19,4 +19,7 @@

         <!-- if 1 ,reboot will reset DEFAULT_LAUNCHER -->
         <bool name="def_lock_default_launcher">false</bool>
    +
    +       <string name="enabled_input_methods" translatable="false">com.android.inputmethod.latin/.LatinIME:com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME:com.google.android.inputmethod.pinyin/.PinyinIME</string>
    +       <string name="def_input_method" translatable="false">com.iflytek.inputmethod.google/com.iflytek.inputmethod.FlyIME</string>
     </resources>

    diff --git a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    index cac5f800bd..f705a895b4 100644
    --- a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    +++ b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
    @@ -2474,6 +2474,11 @@ class DatabaseHelper extends SQLiteOpenHelper {
                 stmt = db.compileStatement("INSERT OR IGNORE INTO secure(name,value)"
                         + " VALUES(?,?);");

    +                       loadStringSetting(stmt, Settings.Secure.ENABLED_INPUT_METHODS,
    +                                       R.string.enabled_input_methods);
    +                       loadStringSetting(stmt, Settings.Secure.DEFAULT_INPUT_METHOD,
    +                                       R.string.def_input_method);
    +
                 loadStringSetting(stmt, Settings.Secure.LOCATION_PROVIDERS_ALLOWED,
                         R.string.def_location_providers_allowed);
    ```

# Delete built-in apps 
## Temporary modification method 
```shell
adb shell setprop persist.sys.root_access 3
adb root
adb remount
adb shell rm -r system/app/APK_NAME/
adb shell reboot
```
or 
```shell
adb shell setprop persist.sys.root_access 3
adb root
adb remount
adb shell rm -r system/priv-app/APK_NAME/
adb shell reboot
```

## Code modification method 
* If the application has source code, comment out the `include $(BUILD_PACKAGE)` in the `Android.mk` file of the application source code 
* If the application is an APK file and has `Android.mk`, comment out `include $(BUILD_PREBUILT)` in the `Android.mk` file 

# Startup application 
## Code modification method 
### Non-launcher 
* Add the following content to the `AndroidManifest.xml` in the application source code 

    ```
    package="com.example.testfile"
    android:sharedUserId="android.uid.system">
    ```

	```
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    ```

    ```
    <receiver android:name="com.example.testfile.BootBroadcastReceiver" >
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <category android:name="android.intent.category.LAUNCHER" />
       </intent-filter>
    </receiver>
    ```
* Add the code for broadcast reception `BootBroadcastReceiver.java`, such as 

    ```
    package com.example.testfile;

    import android.content.BroadcastReceiver;
    import android.content.Context;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.Toast;

    public class BootBroadcastReceiver extends BroadcastReceiver{
        private static final String TAG = "sjft";

        public static final String EXTRA_VOLUME_STATE =
                "android.os.storage.extra.VOLUME_STATE";

        public static final int STATE_UNMOUNTED = 0;
        public static final int STATE_CHECKING = 1;
        public static final int STATE_MOUNTED = 2;
        public static final int STATE_MOUNTED_READ_ONLY = 3;
        public static final int STATE_FORMATTING = 4;
        public static final int STATE_EJECTING = 5;
        public static final int STATE_UNMOUNTABLE = 6;
        public static final int STATE_REMOVED = 7;
        public static final int STATE_BAD_REMOVAL = 8;

        @Override
        public void onReceive(Context context, Intent intent) {
            // TODO Auto-generated method stub
            String action = intent.getAction();
            if (action.equals("android.intent.action.PACKAGE_REPLACED")){
                String packageName = intent.getData().getSchemeSpecificPart();
                Log.v(TAG,"BootBroadcastReceiver packageName:"+packageName);
                if(context.getPackageName().equals(packageName)){
                    Intent launchIntent = new Intent(context, MainActivity.class);//Restart the app 
                    //If you don’t want to write the startup Activity, you can also get the default startup Activity by the following method 
                    //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                    launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    context.startActivity(launchIntent);
                }
            } else if (action.equals("android.intent.action.BOOT_COMPLETED")){
                String packageName = intent.getData().getSchemeSpecificPart();
                Log.v(TAG,"BootBroadcastReceiver packageName:"+packageName);
                if(context.getPackageName().equals(packageName)){
                    Intent launchIntent = new Intent(context, MainActivity.class);//Restart the app 
                    //If you don’t want to write the startup Activity, you can also get the default startup Activity by the following method 
                    //Intent launchIntent = context.getPackageManager().getLaunchIntentForPackage(packageName);
                    launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    context.startActivity(launchIntent);
                }
            } 
        }
    }
    ```

* app system signature ([signature file](system_function.html#android-system-signature-file))  
* start app once
### Launcher
* Delete or not compile the Launcher application in the SDK 
* Add the following content to the first activity started in `AndroidManifest.xml` in the application source code 

    ```
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    ```

# Modification of the default language 
The language support list can be viewed in `build/target/product/languages_full.mk`. 

## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify the value of `ro.product.locale` in the `/system/build.prop` file (Android10 and above: `/vendor/build.prop`) 
	* en-US: English 
	* zh-CN: Chinese 
* System restart 
	```shell
    adb shell reboot
    ```

## Code modification method 
* Modify the value of `PRODUCT_LOCALES` in `build/target/product/full_base.mk` 
	* Chinese 
	
        ```
        PRODUCT_LOCALES := zh_CN
        ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
# Modification of the default time zone 
The time zone support list can be viewed in `frameworks/base/packages/SettingsLib/res/xml/timezones.xml`. 
## Temporary modification method 
* System readable and writable 

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    ```
* Modify the value of `persist.sys.timezone` in the `/system/build.prop` file (Android10 and above: `/vendor/build.prop`) 
	* `Asia/Shanghai`：UTC+8
* System restart 

	```shell
    adb shell reboot
    ```

## Code modification method 
* Android7.1/Android10
    * In the file `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop`, modify the value of `persist.sys.timezone` 
        * `Asia/Shanghai`：UTC+8
* Android11 and above
    * Add the following content in the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file: 
    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.timezone=Asia/Shanghai
    ```
* Delete build.prop in the out directory 
    ```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

# Default ROOT 
## Temporary modification method 
```shell
adb shell setprop persist.sys.root_access 3
```
## Code modification method 
* Android7.1/Android10.0
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

	```
    persist.sys.root_access=3
    ```
* Android11 and above
    * Add the following content in the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.sys.root_access=3
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```

# The default system time is 24 hours 
## Code modification method 
```
diff --git a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
index f95ecc6535..06055dba7b 100644
--- a/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
+++ b/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
@@ -244,4 +244,6 @@
 
     <!-- should show the screenshot button default -->
     <integer name="def_screenshot_button_show">0</integer>
+<!-- value 12/24/null corresponds to setting 12/24/auto -->
+    <string name="def_time_12_24" translatable="false">24</string>
 </resources>
diff --git a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
index b3ff9d08a8..ebdd5d3637 100644
--- a/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
+++ b/frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
@@ -2261,6 +2261,9 @@ class DatabaseHelper extends SQLiteOpenHelper {
    private void loadSystemSettings(SQLiteDatabase db) {
                                     ......
+            
+            loadStringSetting(stmt, Settings.System.TIME_12_24,
+                    R.string.def_time_12_24);
```

# Open network ADB by default 
## Temporary modification method 
### Way one 

* Android7.1
    ```
    adb shell setprop persist.internet.adb.enable 1
    ```
* Android10/Android11/Android12
    ```
    adb shell setprop persist.internet_adb_enable 1
    ```

### Way two
* [Open "Developer Options"](system_function.md#open-developer-options)
* Open `ADB over network` 

	`Settings`-->`Developer options`-->`ADB over network`
## Code modification method 
* Android7.1/Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

    ```
    persist.internet.adb.enable=1
    ```

* Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

    ```
    persist.internet_adb_enable=1
    ```

* Android11/Android12
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.internet_adb_enable=1
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
# The default setting of OTG USB3.0 is devices mode 
## Temporary modification method 
<!--
OTG_MODE: 0
HOST_MODE: 1
SLAVE_MODE: 2
-->
```shell
adb shell setprop persist.usb.mode 2
```
## Code modification method 
* Android7.1/Android10
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/system.prop` file, add the following content: 

	```
    persist.usb.mode=2
    ```
* Android11/Android12
    * In the `device/rockchip/CPU_TYPE/PRODUCT_TYPE/PRODUCT_TYPE.mk` file, add the following content: 

    ```
    PRODUCT_PROPERTY_OVERRIDES += persist.usb.mode=otg
    ```
* Delete build.prop in the out directory 

	```shell
    rm out/target/product/PRODUCT_TYPE/obj/ETC/system_build_prop_intermediates/build.prop
    ```
    
# Turn on or turn off the installation of unknown apps
## Code modification method 
### Android 7.1
In the `frameworks/base/packages/SettingsProvider/res/values/defaults.xml` file, modify the value of `def_install_non_market_apps`. 
* false : Close 
* true : Open 

### Android10 and above 
In Android10, the permanent authorization option of "Allow Unknown Sources" to install applications has been deleted. The switch is no longer found in the system settings. Google will modify the permanent authorization to a separate authorization each time. When the user installs a third party each time The source of the Android software requires manual confirmation of the software permissions. 

# Turn touch sound on or off 
## Code modification method 
In the `frameworks/base/packages/SettingsProvider/res/values/defaults.xml` file, modify the value of `def_sound_effects_enabled`. 
* false : Close
* true : Open

# Modify the desktop layout 
## Code modification method 
After the device is turned on, the program shortcuts, small plug-ins, shortcut navigation bar (HotSeat) and folder modification methods that are loaded on the desktop by default. 

For details, please refer to [the post of the open source community](http://dev.t-firefly.com/forum.php?mod=viewthread&tid=13331&highlight=%D7%C0%C3%E6%B2%BC%BE%D6)。

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
# 
## Temporary modification method 
## Code modification method 
-->
