# PREEMPT_RT

## 简介

**PREEMPT_RT** 是 Linux 内核的实时化方案，通过补丁集并逐步并入主线内核的方式，减少内核中不可抢占区域，使高优先级任务能够以较低延迟抢占内核执行。其核心机制包括将自旋锁改造为可睡眠的 `rt_mutex`、将大部分硬件中断处理线程化，以及将 RCU 回调处理线程化等。该方案通常可提供微秒级的确定性延迟，并与标准 Linux API、C 库和驱动模型保持较好兼容，因而适合大多数工业控制、机器人、自动驾驶等需要实时能力且希望复用 Linux 生态的场景。

## PREEMPT 内核补丁

根据当前内核版本选择对应版本的 Preempt 内核补丁。

### 补丁来源

1. Rockchip SDK: 相关补丁位于 `$sdk/docs/Patches/Real-Time-Performance/PREEMPT_RT/` 目录下。
2. kernel rt 项目: 可前往 https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt 下载与自己内核对应的补丁包来使用。

### 应用补丁

以RK3588为例，打入内核对应版本的 rt 补丁。

```bash
$ cd kernel
$ patch -p1 < ../docs/Patches/Real-Time-Performance/PREEMPT_RT/kernel-6.1/kernel-6.1.118/0001-patch-6.1.99-rt36-on-rockckip-base-5c295c763974.patch
```

## 内核编译

```bash
$ cd $sdk/kernel/
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config rockchip_rt.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

> 备注：此处以 RK3588 为例。其它芯片平台编译内核时，内核配置需要加上 `rockchip_rt.config`。

## 测试

烧录 `boot.img` 后，安装并使用 `cyclictest` 测试实时性能：

```bash
$ apt update && apt install -y rt-tests
$ cyclictest -c 0 -m -t -a -p99 -D12h
```
