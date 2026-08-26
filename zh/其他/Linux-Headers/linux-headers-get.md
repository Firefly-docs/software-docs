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
