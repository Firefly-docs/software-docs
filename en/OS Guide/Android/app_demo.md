# Overview 
App Demo is generally built into the firmware. For different devices, the built-in App Demo will be different. The specific firmware burned by the actual device shall prevail. 

# FireflyDemo
The FireflyDemo application can quickly confirm the number of decoding channels supported by the Firefly platform series products, and supports local and rtsp streaming playback. 

For details, please refer to [Efficiently confirm the number of video decoding channels](http://www.t-firefly.com/doc/case/443.html)。

# firefly_sdkapi_demo
For details, please refer to [FIREFLYAPI description](firefly_api.html)。

**NOTE:** Support the full range of Firefly products

# Rmsl3DCameraIQCTest
Used for aging test of RMSL201-1301 structured light module. 

For details, please refer to [RMSL201-1301 structured light module](https://wiki.t-firefly.com/en/Face-RK3399/module_camera.html#rmsl201-1301-structured-light-module)。

**NOTE:** Only supports Android 7.1/Android10

# RmslPreview
Called by Rmsl3DCameraIQCTest

For details, please refer to [RMSL201-1301 structured light module](https://wiki.t-firefly.com/en/Face-RK3399/module_camera.html#rmsl201-1301-structured-light-module)。

**NOTE:** Only supports Android 7.1/Android10

# CAEDemo
The Firefly Smart Voice Kit supports the iFLYTEK AIUI cloud service, and uses the CAEDemo program to implement demonstration functions such as voice recognition, keyword wake-up, noise reduction, and echo cancellation on the smart voice kit.

For details, please refer to [Smart Voice CAEDemo Program](http://www.t-firefly.com/doc/case/452.html)。

**NOTE:** Only supports Android 7.1/Android10

# HDMI IN
HDMI IN function, RK3399 and RK3288 the main function is to collect the signal from HDMI IN and convert it to MIPI signal to the main controller, and its data format is YUV422 8bit.Among them, "I" means interlaced scanning display mode, and "P" means progressive scanning display mode. 
* RK3399 HDMI IN input resolution support list 
    * 640X480   P
    * 720X480   I/P
    * 720X576   I/P
    * 1280X720  P
    * 1920X1080 I/P

**NOTE:** supports Android 7.1

* RK3288 HDMI IN input resolution support list
    * 1280X720  P
    * 1920X1080 P

**NOTE:** supports Android 5.1

* [RK3588 HDMI IN support](https://wiki.t-firefly.com/en/Core-3588J/usage_hdmiin.html)

# ScheduleOnOff
OS power on or off by alarm.

**NOTE:** Firmware is built-in by default, if not, please contact commerce `sales@t-firefly.com`。

<!--
# Face recognition 
Through the face recognition SDK, the intelligent transformation of the access control system can be easily carried out, and the identification and access control of personnel can be quickly and accurately performed by simply "swiping the face". From collecting face photos, comparing faces, and obtaining results, the whole process is automated without manual intervention. 

For details, please refer to [Face Recognition Application Development](http://wiki.t-firefly.com/Face-RK3399/quickstart.html)。
-->
