# linux-headers

linux-headers 包含各种头文件，可以让设备具有本地编译驱动的能力。

## 获取方法

### 下载

1. Kernel 5.10 or Kernel 4.19 内核 linux-headers，请到 Firefly 官方[《资料下载》](https://www.t-firefly.com/doc/download/107.html)页面下载。选择板卡后一般在“**资源**”处，名称为 **linux-headers**。

2. Kernel 6.1 版本的 SDK 默认会编译 linux-headers 到 extboot 中，在设备的 /boot/ 目录下可以看到 linux-headers-6.1-arm64_arm64.deb

### 制作

官方提供的 headers 版本和实际固件可能有差异，并且有定制需求的客户也无法使用，因此建议通过 SDK 制作：

首先准备环境、获取 SDK、编译前配置，请前往不同板卡的维基查看，接下来在 SDK 根目录进行编译：
```bash
./build.sh kerneldeb
```
生成的文件会在 SDK 根目录：
```
linux-headers-x.xx.xxx_x.xx.xxx-xxx_arm64.deb
```
## 安装
以下以 ROC-RK3568-PC 安装为例：

将得到的 deb 包放入设备中，然后安装，对于 headers，安装后还需要进行编译处理：

```shell
# kernel 5.10 or kernel 4.19 linux-headers
sudo apt install ./linux-headers-4.19.172_4.19.172-189_arm64.deb
sudo apt install -y build-essential python libssl-dev # 准备编译环境
cd /usr/src/linux-headers-4.19.172
make headers_check
make headers_install
make scripts # 可能会出错，如果出错在 tools，可以直接忽略，安装完成

# kernel 6.1 linux-headers
sudo apt install ./linux-headers-6.1-arm64_arm64.deb
cd /usr/src/linux-headers-6.1-arm64
./scripts/builddtb.sh
```

## 编译 DTS
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
