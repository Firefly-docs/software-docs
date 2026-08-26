# PREEMPT_RT

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
