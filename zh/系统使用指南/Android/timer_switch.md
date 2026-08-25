# 定时开关机
* 设备要有 RTC 芯片
\t* RK808
\t* HYM8563
* 设备常供电

可以直接使用 cat 和 echo 操作`/sys/class/rtc/rtc0`下面的接口。

## 设置开机时间
比如设置120秒后开机
```shell
#120秒后定时开机
echo +120 >  /sys/class/rtc/rtc0/wakealarm
# 查看开机时间
cat /sys/class/rtc/rtc0/wakealarm
#关机
reboot -p
```
<!--
# NPU 的使用

NPU使用详细参考对应机型

| 主控 | 机型 |
| ---- | ---- |
| RK3399 | [AIO-3399C(AI)](https://wiki.t-firefly.com/AIO-3399C/usage_npu.html) |
| RK3399Pro | [AIO-3399Pro-JD4](https://wiki.t-firefly.com/AIO-3399Pro-JD4/usage_npu.html) |
| RK3566 | [ROC-RK3566-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3566-PC/usage_npu.html) |
| RK3568 | [AIO-3568J](https://wiki.t-firefly.com/Core-3568J/usage_npu.html), [ROC-RK3568-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3568-PC/usage_npu.html) |
| RK3588 | [ITX-3588J](https://wiki.t-firefly.com/zh_CN/Core-3588J/usage_npu.html), [ROC-RK3588S-PC](https://wiki.t-firefly.com/zh_CN/ROC-RK3588S-PC/usage_npu.html) |
-->
