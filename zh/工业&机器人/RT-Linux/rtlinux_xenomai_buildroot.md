# 使用 Buildroot 配置 Xenomai

## Xenomai

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
