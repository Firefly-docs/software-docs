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

