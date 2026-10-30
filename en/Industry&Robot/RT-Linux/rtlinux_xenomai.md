# Xenomai

Xenomai is a real-time extension framework for Linux. It traditionally uses a dual-kernel architecture: a high-priority real-time core, such as Cobalt or EVL, runs alongside the general-purpose Linux kernel and directly handles real-time tasks, interrupts, and time-critical activities. Xenomai 3 also provides Mercury mode, which allows the Xenomai API to run on a PREEMPT_RT kernel. The framework can provide very low jitter and strong hard real-time determinism, with sub-microsecond jitter on some platforms. However, real-time applications need to use Xenomai APIs, and hardware drivers often need to be adapted to a real-time driver model such as RTDM, which increases development and maintenance complexity.

## Dovetail Kernel Patches

Select the Dovetail kernel patch that matches the current kernel version.

### Patch Sources

1. Rockchip SDK: the patches are located in `$sdk/docs/Patches/Real-Time-Performance/XENOMAI/`.
2. Xenomai project: download a version matching your kernel from https://source.denx.de/Xenomai/linux-dovetail.

We recommend first looking in the SDK for a patch matching your kernel.

### Apply the Patch

Using RK3588 as an example, apply the Dovetail kernel patch corresponding to the kernel version:

```
cd kernel
patch -p1 < ../docs/Patches/Real-Time-Performance/XENOMAI/kernel-6.1/6.1.118/0001-dovetail-core-6.1.97-on-rockchip-base-bb98340ac56e.patch
```

## Using Buildroot

### Enable Xenomai and Build rootfs.img

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

### Apply the Xenomai Cobalt Patch

Apply the Xenomai Cobalt patch to the kernel:

```bash
$ cd $sdk/kernel
$ ../buildroot/output/rockchip_rk3588/build/xenomai-v3.2.5/scripts/prepare-kernel.sh --arch=arm64
```

### Build the Kernel

The following is the Linux 6.1 build command, using RK3588 as an example:

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

### Flash Images

Flash `boot.img` and `rootfs.img`.

### Test Real-Time Performance

Calibrate `latency`:

```bash
$ echo 0 > /proc/xenomai/latency
```

Use `cyclictest` for testing:

```bash
$ ./usr/demo/cyclictest -c 0 -m -n -t -p99 -D12h
```

## Using Debian / Ubuntu

### Apply the Xenomai Cobalt Patch

Download the Xenomai source code under `$sdk`:

```bash
$ cd $sdk
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

Apply the Xenomai Cobalt patch to the kernel:

```bash
$ cd $sdk/kernel
$ ../xenomai/scripts/prepare-kernel.sh --arch=arm64
```

### Build the Kernel

The following is the Linux 6.1 build command, using RK3588 as an example:

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

### Flash Images

Flash `boot.img`.

### Install Xenomai on the System

Download the Xenomai source code on the system:

```bash
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

Build and install it into the `build` directory:

```bash
$ mkdir -p build
$ ./scripts/bootstrap
$ ./configure --with-core=cobalt --enable-pshared --enable-smp
$ make -j$(nproc) DESTDIR=$(pwd)/build install
```

After the build completes, the Xenomai files are generated in the `build` directory. Package this directory or copy it to `/usr` on the system.

### Configure Environment Variables

```bash
$ export XENOMAI_ROOT_DIR=/usr/xenomai
$ export XENOMAI_PATH=/usr/xenomai
$ export PATH=$PATH:$XENOMAI_PATH/bin:$XENOMAI_PATH/sbin
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:$XENOMAI_PATH/lib/pkgconfig
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$XENOMAI_PATH/lib
$ export OROCOS_TARGET=xenomai
```

### Test Real-Time Performance

Calibrate `latency`:

```bash
$ echo 0 > /proc/xenomai/latency
```

Use `latency` for testing:

```bash
$ latency -t0 -p 100 -P 99 -h -g result.txt
```
