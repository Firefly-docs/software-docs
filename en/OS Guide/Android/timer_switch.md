# Timer switch 
* The device must have an RTC chip 
\t* RK808
\t* HYM8563
* Equipment is always powered 

You can directly use cat and echo to operate the interface under `/sys/class/rtc/rtc0`. 

## Set boot time 
For example, set it to boot after 120 seconds 
```shell
# Turn on regularly after 120 seconds 
echo +120 >  /sys/class/rtc/rtc0/wakealarm
# View boot time 
cat /sys/class/rtc/rtc0/wakealarm
# Shut down 
reboot -p
```
<!--
# Use of NPU 

| CPU | Board |
| ---- | ---- |
| RK3399 | [AIO-3399C(AI)](https://wiki.t-firefly.com/en/AIO-3399C/usage_npu.html) |
| RK3399Pro | [AIO-3399Pro-JD4](https://wiki.t-firefly.com/en/AIO-3399Pro-JD4/usage_npu.html) |
| RK3566 | [ROC-RK3566-PC](https://wiki.t-firefly.com/en/ROC-RK3566-PC/usage_npu.html) |
| RK3568 | [AIO-3568J](https://wiki.t-firefly.com/en/Core-3568J/usage_npu.html), [ROC-RK3568-PC](https://wiki.t-firefly.com/en/ROC-RK3568-PC/usage_npu.html) |
| RK3588 | [ITX-3588J](https://wiki.t-firefly.com/en/Core-3588J/usage_npu.html), [ROC-RK3588S-PC](https://wiki.t-firefly.com/en/ROC-RK3588S-PC/usage_npu.html) |
-->
