# PREEMPT_RT

## Introduction

**PREEMPT_RT** is a real-time solution for the Linux kernel. It reduces non-preemptible sections through a patch set that is gradually being merged into the mainline kernel, allowing high-priority tasks to preempt kernel execution with low latency. Its key mechanisms include converting spinlocks to sleepable `rt_mutex` locks, threading most hardware interrupt handlers, and threading RCU callbacks. It typically provides deterministic latency at the microsecond level while remaining highly compatible with standard Linux APIs, C libraries, and the driver model. This makes it suitable for most industrial control, robotics, and autonomous driving scenarios that require real-time capability while reusing the Linux ecosystem.

## PREEMPT Kernel Patches

Select the PREEMPT kernel patch that matches the current kernel version.

### Patch Sources

1. Rockchip SDK: the patches are located in `$sdk/docs/Patches/Real-Time-Performance/PREEMPT_RT/`.
2. Kernel RT project: download a patch package matching your kernel from https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt.

### Apply the Patch

Using RK3588 as an example, apply the RT patch corresponding to the kernel version:

```bash
$ cd kernel
$ patch -p1 < ../docs/Patches/Real-Time-Performance/PREEMPT_RT/kernel-6.1/kernel-6.1.118/0001-patch-6.1.99-rt36-on-rockckip-base-5c295c763974.patch
```

## Build the Kernel

```bash
$ cd $sdk/kernel/
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config rockchip_rt.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

> Note: RK3588 is used as an example here. When building the kernel for other chip platforms, add `rockchip_rt.config` to the kernel configuration.

## Test

After flashing `boot.img`, install and use `cyclictest` to test real-time performance:

```bash
$ apt update && apt install -y rt-tests
$ cyclictest -c 0 -m -t -a -p99 -D12h
```
