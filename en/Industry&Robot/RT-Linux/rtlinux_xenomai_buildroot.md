# Xenomai with Buildroot

## Xenomai

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
