# RTLinux 实时系统开发指南

## 前言

本文介绍 Linux 内核实时性补丁的基本使用方法，帮助开发者快速了解并使用实时系统。

---

## 概要

根据当前内核版本选择对应的实时系统内核补丁。可通过查看 `kernel/Makefile` 确认内核版本：

```shell
# SPDX-License-Identifier: GPL-2.0
VERSION = 6
PATCHLEVEL = 1
SUBLEVEL = 118
...
```

## PREEMPT_RT

### 内核打补丁

根据当前内核版本选择对应补丁。

#### Rockchip

补丁位于 `$sdk/docs/Patches/Real-Time-Performance/PREEMPT_RT/` 目录下。

#### Other

可前往[kernel](https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/)下载与自己内核对应的补丁包来使用。

### 编译内核

```bash
$ cd $sdk/kernel/
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config rockchip_rt.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

> 备注：此处以 RK3588 为例。其它芯片平台编译内核时，内核配置需要加上 `rockchip_rt.config`。

### 烧录并测试实时性能

烧录 `boot.img` 后，使用 `cyclictest` 测试实时性能：

```bash
$ cyclictest -c 0 -m -t -a -p99 -D12h
```

## Xenomai

### 内核打补丁

根据当前内核版本选择对应补丁。

#### Rockchip

补丁位于 `$sdk/docs/Patches/Real-Time-Performance/XENOMAI/` 目录下。

#### Other

可前往[xenomai](https://source.denx.de/Xenomai/linux-dovetail)下载与自己内核对应版本来使用。

### Buildroot

#### 打开 Xenomai 配置并编译 rootfs.img

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_3_2=y
BR2_PACKAGE_XENOMAI_VERSION="v3.2.2"
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

Linux 6.1 版本使用 Xenomai v3.2.4，Buildroot 需要包含 `0001-xenomai-Support-3.2.4.patch`：

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_3_2_4=y
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

Linux 6.1.118 及以上版本的 Buildroot 默认支持 Xenomai 3.2.5，不需要额外打补丁：

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_LATEST_VERSION=y
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

#### 添加 Xenomai 到内核

```bash
$ cd $sdk/kernel
$ ../buildroot/output/rockchip_rk3588/build/xenomai-v3.2.5/scripts/prepare-kernel.sh --arch=arm64
```

#### 编译内核

Linux 6.1 编译命令如下，此处以 RK3588 为例：

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

#### 烧录镜像

烧录 `boot.img` 和 `rootfs.img`。

#### 测试实时性能

校准 `latency`：

```bash
$ echo 0 > /proc/xenomai/latency
```

使用 `cyclictest` 测试：

```bash
$ ./usr/demo/cyclictest -c 0 -m -n -t -p99 -D12h
```

### Debian / Ubuntu

#### 添加 Xenomai 到内核

在 `$sdk` 目录下下载 Xenomai 源码：

```bash
$ cd $sdk
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

把 Xenomai 系统打到内核上：

```bash
$ cd $sdk/kernel
$ ../xenomai/scripts/prepare-kernel.sh --arch=arm64
```

#### 编译内核

Linux 6.1 编译命令如下，此处以 RK3588 为例：

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

#### 烧录镜像

烧录 `boot.img`。

#### 安装 Xenomai 到系统

在系统中下载 Xenomai 源码：

```bash
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

编译并安装到 `build` 目录：

```bash
$ mkdir -p build
$ ./scripts/bootstrap
$ ./configure --with-core=cobalt --enable-pshared --enable-smp
$ make -j$(nproc) DESTDIR=$(pwd)/build install
```

编译完成后会在 `build` 目录下生成 Xenomai 文件，将该目录打包或者复制到系统的 `/usr` 目录下即可。

#### 配置环境变量

```bash
$ export XENOMAI_ROOT_DIR=/usr/xenomai
$ export XENOMAI_PATH=/usr/xenomai
$ export PATH=$PATH:$XENOMAI_PATH/bin:$XENOMAI_PATH/sbin
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:$XENOMAI_PATH/lib/pkgconfig
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$XENOMAI_PATH/lib
$ export OROCOS_TARGET=xenomai
```

#### 测试实时性能

校准 `latency`：

```bash
$ echo 0 > /proc/xenomai/latency
```

使用 `latency` 测试：

```bash
$ latency -t0 -p 100 -P 99 -h -g result.txt
```

## 注意事项

### RK3568 需要使用 RT 版本的 BL31

RK3568 使用 RT 版本的 BL31 可获得更好的实时性能。

编译命令：

```shell
$ cd $sdk/uboot
$ ./make.sh rk3568-rt
```

烧录 `miniloader.bin` 和 `uboot.img`。

开机过程中出现 `cache_write_streaming_cfg` 相关打印，说明已经使用 RT 版本的 BL31：

### RK3568 提高实时性的做法

#### cache 分片

ARM Cortex-A55 架构支持对 L3 空间进行划分。Cortex-A55 L3 内存空间可划分为 4 块，可以配置每个 CPU 使用其中的哪几块。该配置在 `rkbin/RKBOOT/RK3568MINIALL.ini` 文件中设置：

```c
[BOOT1_PARAM]

WORD_3=0xcc33
```

`WORD_3=0xcc33` 表示以 P0、P1、P2、P3 标记 L3 的 4 块空间时：

- CPU0、CPU1 共享 L3 的 P0、P1。
- CPU2、CPU3 共享 L3 的 P2、P3。

`WORD_3` 配置值说明如下：

- bit0~bit3：分配给 CPU0 的 4 份 L3 mask bit。bit0 为 1 表示 L3 的第一份分给 CPU0，bit1 为 1 表示 L3 的第二份分给 CPU0，以此类推。
- bit4~bit7：分配给 CPU1 的 4 份 L3 mask bit。
- bit8~bit11：分配给 CPU2 的 4 份 L3 mask bit。
- bit12~bit15：分配给 CPU3 的 4 份 L3 mask bit。

配置后可通过以下开机日志确认：

```shell
INFO: L3 cache partition cfg-cc33
```

#### 隔离核心

在 `bootargs` 中添加 `isolcpus=3 nohz_full=3`，将 CPU3 隔离出来，使其不参与系统任务调度，并作为实时核心使用。

```diff
diff --git a/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi b/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
index c7e309645099b..28fac4880744d 100644
--- a/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
+++ b/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
@@ -13,7 +13,7 @@ aliases {
        };
        chosen: chosen {
-               bootargs = "earlycon=uart8250,mmio32,0xfe660000 console=ttyFIQ0 root=PARTUUID=614e0000-0000 rw rootwait";
+               bootargs = "earlycon=uart8250,mmio32,0xfe660000 isolcpus=3 nohz_full=3 console=ttyFIQ0 root=PARTUUID=614e0000-0000 rw rootwait";
        };
        fiq-debugger {
```

#### 实时任务绑定到实时核上运行

将 `cyclictest` 绑定到 CPU3 上运行，测试实时性能：

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```

> 注：`ps -eo pid,psr,comm | grep cyclictest` 可以查看 `cyclictest` 是否绑定在 CPU3。
