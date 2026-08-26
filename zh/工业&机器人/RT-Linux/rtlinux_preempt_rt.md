# PREEMPT_RT

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
