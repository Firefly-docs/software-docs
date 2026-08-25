# 删除内置应用
## 临时修改的方式
```shell
adb shell setprop persist.sys.root_access 3
adb root
adb remount
adb shell rm -r system/app/APK_NAME/
adb shell reboot
```
或者
```shell
adb shell setprop persist.sys.root_access 3
adb root
adb remount
adb shell rm -r system/priv-app/APK_NAME/
adb shell reboot
```

## 代码修改的方式
* 如果应用有源代码，在应用源代码的`Android.mk`文件里注释掉`include $(BUILD_PACKAGE)`
* 如果应用是 APK 文件且有`Android.mk`，则在`Android.mk`文件里注释掉`include $(BUILD_PREBUILT)`

