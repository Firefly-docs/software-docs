# 概述
App Demo 一般内置在固件里，对于不同的设备，内置的 App Demo 会有些区别，具体以实际设备烧录的固件为准。

# FireflyDemo
FireflyDemo 应用可快速确认 Firefly 平台系列产品支持的解码路数，并支持本地和 rtsp 播放流播放。

详细参考[高效确认视频解码路数](http://www.t-firefly.com/doc/case/443.html)。

**NOTE:** 支持Firefly全系列产品

# firefly_sdkapi_demo
详细参考[FIREFLYAPI 说明](firefly_api.md)。

**NOTE:** 仅支持Android 7.1/Android10

# Rmsl3DCameraIQCTest
用于 RMSL201-1301 结构光模组的老化测试。

详细参考[RMSL201-1301结构光模组](http://wiki.t-firefly.com/Face-RK3399/module_camera.html#rmsl201-1301-jie-gou-guang-mo-zu)。

**NOTE:** 仅支持Android 7.1/Android10

# RmslPreview
供 Rmsl3DCameraIQCTest 调用

详细参考[RMSL201-1301结构光模组](http://wiki.t-firefly.com/Face-RK3399/module_camera.html#rmsl201-1301-jie-gou-guang-mo-zu)。

**NOTE:** 仅支持Android 7.1/Android10

# CAEDemo
Firefly 智能语音套件支持科大讯飞 AIUI 云服务，通过 CAEDemo 程序在智能语音套件上实现语音识别、关键词唤醒、降噪、回声消除等示范功能。

详细参考[智能语音CAEDemo程序](http://www.t-firefly.com/doc/case/452.html)。

**NOTE:** 仅支持Android 7.1/Android10

# HDMI IN
HDMI IN 功能，RK3399和RK3288主要作用是采集 HDMI IN 过来的信号转换为 MIPI 信号给主控，其数据格式是 YUV422 8bit。
如下格式中 I 表示交错式扫描显示方式，P 表示逐行扫描显示方式。

* RK3399 HDMI IN 输入分辨率支持列表
    * 640X480   P
    * 720X480   I/P
    * 720X576   I/P
    * 1280X720  P
    * 1920X1080 I/P

**NOTE:** 支持Android 7.1

* RK3288 HDMI IN 输入分辨率支持列表
    * 1280X720  P
    * 1920X1080 P

**NOTE:** 支持Android 5.1

* [RK3588 HDMI IN 支持格式和使用方式](https://wiki.t-firefly.com/zh_CN/Core-3588J/usage_hdmiin.html)


# ScheduleOnOff
定时开关机演示APK。

**NOTE:** 固件默认内置，如果没有，请联系商务`sales@t-firefly.com`。

<!--
# 人脸识别
通过人脸识别 SDK，可轻松地进行门禁系统的智能化改造，只需“刷脸”就能快速准确进行人员的识别及出入控 制。从采集人脸照片、人脸比对、并获取结果，全程自动化操作，无需人工干预。

详细参考[人脸识别应用开发](http://wiki.t-firefly.com/Face-RK3399/quickstart.html)。
-->
