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


