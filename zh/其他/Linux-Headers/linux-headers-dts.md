# 编译 DTS

Kernel 6.1 SDK 对应的 Linux-headers 已经打包了 DTS，客户可以直接在板子上快速完成 DTS 修改和编译，不需要再另外在电脑搭建开发环境。
```shell
# 以ROC-RK3588S-PC 修改dts 切换 PCIE/SATA 为例:
cd /usr/src/linux-headers-6.1-arm64/
vim arch/arm64/boot/dts/rockchip/rk3588-firefly-roc-rk3588s-pc.dts

#define FF_SATA0 // M.2 slot, mux with pcie2x1l2
//#define FF_PCIE2_1L2 // M.2 slot, mux with sata0

=>

//#define FF_SATA0 // M.2 slot, mux with pcie2x1l2
#define FF_PCIE2_1L2 // M.2 slot, mux with sata0

./scripts/builddtb.sh -s

reboot
```
