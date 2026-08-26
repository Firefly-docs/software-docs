# Xenomai with Debian or Ubuntu

## Xenomai

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
