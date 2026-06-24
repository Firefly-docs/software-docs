# linux-headers

linux-headers include header files, can make the device able to compile drivers.

## How to get

### Download from Firefly

Kernel 5.10 or Kernel 4.19 Linux-Headers: For the package download of each board, please go to the official Firefly *[Download](http://en.t-firefly.com/doc/download/61.html)* page to download. After selecting the board, click the **Download** page, it is generally in the **Resources** section, and the name is **linux-headers**.

Kernel 6.1 Linux-Headers: By default, the SDK for kernel 6.1 will compile linux-headers into extboot.img. You can find linux-headers-6.1-arm64_arm64.deb in the /boot/ directory of the device.

### Build from SDK

The version of headers and image downloaded above may mismatch your firmware, and they are not helpful with customization needs. So build them from SDK is recommended.

Prepare environment, get SDK and comfigure for compile, please check the specific deivce's wiki.

Build under SDK root directory:
```bash
./build.sh kerneldeb
```
Output files are in SDK root directory:
```
linux-headers-x.xx.xxx_x.xx.xxx-xxx_arm64.deb
```

## Install

The following takes the installation of ROC-RK3568-PC as an example:

Put deb packages in device to install, for headers, you have to compile them after installation
```shell
# Compile Kernel 5.10 or 4.19 Linux-Headers
sudo apt install ./linux-headers-4.19.172_4.19.172-189_arm64.deb
sudo apt install -y build-essential python libssl-dev # Prepare
cd /usr/src/linux-headers-4.19.172
make headers_check
make headers_install
make scripts # make scripts may go wrong but it doesn't matter

# Compile Kernel 6.1 Linux-Headers
sudo apt install ./linux-headers-6.1-arm64_arm64.deb
cd /usr/src/linux-headers-6.1-arm64
./scripts/builddtb.sh
```

## Compile DTS
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
