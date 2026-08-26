# Compile DTS

The Linux headers corresponding to the SDK have already packaged DTS. Customers can quickly modify and compile 

DTS directly on the board without having to set up a separate development environment on the computer.
```shell
# Take ROC-RK3588S-PC to modify DTS to switch PCIE/SATA as an example:
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
