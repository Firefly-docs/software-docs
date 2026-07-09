# RTLinux Real-Time System Development Guide

---

## Preface

This document describes the basic usage of Linux kernel real-time patches and helps developers quickly understand and use a real-time system.

---

## Contents

[TOC]

---

## Overview

Select the corresponding real-time system kernel patch based on the current kernel version. You can check the kernel version in `kernel/Makefile`:

```shell
# SPDX-License-Identifier: GPL-2.0
VERSION = 6
PATCHLEVEL = 1
SUBLEVEL = 118
...
```

## PREEMPT_RT

### Patch the Kernel

Select the corresponding patch based on the current kernel version.

#### Rockchip

The patches are located in `$sdk/docs/Patches/Real-Time-Performance/PREEMPT_RT/`.

#### Other

You can download a patch package matching your kernel from [kernel](https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/).

### Build the Kernel

```bash
$ cd $sdk/kernel/
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config rockchip_rt.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

> Note: RK3588 is used as an example here. When building the kernel for other chip platforms, add `rockchip_rt.config` to the kernel configuration.

### Flash and Test Real-Time Performance

After flashing `boot.img`, use `cyclictest` to test real-time performance:

```bash
$ cyclictest -c 0 -m -t -a -p99 -D12h
```

## Xenomai

### Patch the Kernel

Select the corresponding patch based on the current kernel version.

#### Rockchip

The patches are located in `$sdk/docs/Patches/Real-Time-Performance/XENOMAI/`.

#### Other

You can download a version matching your kernel from [xenomai](https://source.denx.de/Xenomai/linux-dovetail).

### Buildroot

#### Enable Xenomai and Build rootfs.img

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_3_2=y
BR2_PACKAGE_XENOMAI_VERSION="v3.2.2"
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

Linux 6.1 uses Xenomai v3.2.4. Buildroot needs to include `0001-xenomai-Support-3.2.4.patch`:

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_3_2_4=y
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

Buildroot for Linux 6.1.118 and later supports Xenomai 3.2.5 by default, so no extra patch is required:

```bash
BR2_PACKAGE_XENOMAI=y
BR2_PACKAGE_XENOMAI_LATEST_VERSION=y
BR2_PACKAGE_XENOMAI_COBALT=y
BR2_PACKAGE_XENOMAI_TESTSUITE=y
BR2_PACKAGE_XENOMAI_ADDITIONAL_CONF_OPTS="--enable-demo"
```

#### Add Xenomai to the Kernel

```bash
$ cd $sdk/kernel
$ ../buildroot/output/rockchip_rk3588/build/xenomai-v3.2.5/scripts/prepare-kernel.sh --arch=arm64
```

#### Build the Kernel

The following is the build command for Linux 6.1, using RK3588 as an example:

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

#### Flash Images

Flash `boot.img` and `rootfs.img`.

#### Test Real-Time Performance

Calibrate `latency`:

```bash
$ echo 0 > /proc/xenomai/latency
```

Use `cyclictest` for testing:

```bash
$ ./usr/demo/cyclictest -c 0 -m -n -t -p99 -D12h
```

### Debian / Ubuntu

#### Add Xenomai to the Kernel

Download the Xenomai source code under `$sdk`:

```bash
$ cd $sdk
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

Apply the Xenomai system to the kernel:

```bash
$ cd $sdk/kernel
$ ../xenomai/scripts/prepare-kernel.sh --arch=arm64
```

#### Build the Kernel

The following is the build command for Linux 6.1, using RK3588 as an example:

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

#### Flash Images

Flash `boot.img`.

#### Install Xenomai to the System

Download the Xenomai source code on the target system:

```bash
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

Build and install it to the `build` directory:

```bash
$ mkdir -p build
$ ./scripts/bootstrap
$ ./configure --with-core=cobalt --enable-pshared --enable-smp
$ make -j$(nproc) DESTDIR=$(pwd)/build install
```

After the build is complete, the Xenomai files are generated in the `build` directory. Package this directory or copy it to `/usr` on the system.

#### Configure Environment Variables

```bash
$ export XENOMAI_ROOT_DIR=/usr/xenomai
$ export XENOMAI_PATH=/usr/xenomai
$ export PATH=$PATH:$XENOMAI_PATH/bin:$XENOMAI_PATH/sbin
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:$XENOMAI_PATH/lib/pkgconfig
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$XENOMAI_PATH/lib
$ export OROCOS_TARGET=xenomai
```

#### Test Real-Time Performance

Calibrate `latency`:

```bash
$ echo 0 > /proc/xenomai/latency
```

Use `latency` for testing:

```bash
$ latency -t0 -p 100 -P 99 -h -g result.txt
```

## Notes

### RK3568 Requires the RT Version of BL31

Using the RT version of BL31 on RK3568 provides better real-time performance.

Build command:

```shell
$ cd $sdk/uboot
$ ./make.sh rk3568-rt
```

Flash `miniloader.bin` and `uboot.img`.

If `cache_write_streaming_cfg` related messages appear during boot, the RT version of BL31 is being used.

### Methods to Improve Real-Time Performance on RK3568

#### Cache Partitioning

The ARM Cortex-A55 architecture supports L3 space partitioning. Cortex-A55 L3 memory can be divided into 4 parts, and each CPU can be configured to use specific parts. Configure this in `rkbin/RKBOOT/RK3568MINIALL.ini`:

```c
[BOOT1_PARAM]

WORD_3=0xcc33
```

`WORD_3=0xcc33` means the 4 L3 parts are labeled P0, P1, P2, and P3 as follows:

- CPU0 and CPU1 share L3 P0 and P1.
- CPU2 and CPU3 share L3 P2 and P3.

The `WORD_3` configuration value is described below:

- bit0~bit3: the mask bits of the 4 L3 parts allocated to CPU0. bit0 set to 1 means the first L3 part is allocated to CPU0; bit1 set to 1 means the second L3 part is allocated to CPU0, and so on.
- bit4~bit7: the mask bits of the 4 L3 parts allocated to CPU1.
- bit8~bit11: the mask bits of the 4 L3 parts allocated to CPU2.
- bit12~bit15: the mask bits of the 4 L3 parts allocated to CPU3.

After configuration, confirm it through the following boot log:

```shell
INFO: L3 cache partition cfg-cc33
```

#### Isolate a Core

Add `isolcpus=3 nohz_full=3` to `bootargs` to isolate CPU3 so that it does not participate in system task scheduling and can be used as the real-time core.

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

#### Bind Real-Time Tasks to the Real-Time Core

Bind `cyclictest` to CPU3 to test real-time performance:

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```

> Note: Use `ps -eo pid,psr,comm | grep cyclictest` to check whether `cyclictest` is bound to CPU3.
