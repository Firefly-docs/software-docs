# 概述
## 适用范围
* RK3399 Android7.1 Industry
* RK3399 Android10
* RK356X Android11
* RK3588 Android12

对于其他 Android 的版本，可供参考。
<br>
<br>

## 修改方式
Android 系统定制，一般有**临时修改的方式**和**代码修改的方式**。
* 临时修改的方式：一般可以通过调试串口或者 ADB 对设备进行操作。
* 代码修改的方式：修改完代码后，需要进行<font color=#FF0000> SDK 的编译</font>以及<font color=#FF0000>固件的烧录</font>。

其中，*SDK 的编译*或者*固件的烧录*[参考WiKi教程](http://wiki.t-firefly.com/)。

## 名词解释
* `CPU_TYPE`：表示主控芯片，比如rk3288，rk3399，rk3399pro, rk356x, rk3588
* `PRODUCT_TYPE`：表示产品类型，比如rk3399_firefly，rk3399_firefly_aio，rk3399_firefly_aioc_ai

## 定制说明
对于系统的定制的内容，在Android12 Android11, Android10.0 和 Android7.1 Industry会有部分内容不相同，对于这部分内容会对其区分,而没有区分的内容表示兼容。

