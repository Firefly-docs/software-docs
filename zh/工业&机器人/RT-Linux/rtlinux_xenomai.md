# Xenomai

Xenomai 是面向 Linux 的实时扩展框架，传统上采用双内核架构：在通用 Linux 内核之外运行一个高优先级的实时核心（如 Cobalt 或 EVL），由实时核心直接处理实时任务、中断和时间关键活动。Xenomai 3 亦提供 Mercury 模式，使 Xenomai API 可运行于 PREEMPT_RT 内核之上。该框架可提供极低的抖动和较强的硬实时确定性，部分平台可达亚微秒级抖动；但实时应用需使用 Xenomai 提供的 API，硬件驱动常需基于 RTDM 等实时驱动模型适配，开发与维护复杂度相对较高。

## Dovetail 内核补丁

根据当前内核版本选择对应版本的 Dovetail 内核补丁。

### 补丁来源

1. Rockchip SDK：相关补丁位于 `$sdk/docs/Patches/Real-Time-Performance/XENOMAI/` 目录下。
2. xenomai 官方：可前往 https://source.denx.de/Xenomai/linux-dovetail 下载与自己内核对应版本来使用。

### 应用补丁

以RK3588为例，打入与内核对应版本的Dovetail 内核补丁：

```
cd kernel
patch -p1 < ../docs/Patches/Real-Time-Performance/XENOMAI/kernel-6.1/6.1.118/0001-dovetail-core-6.1.97-on-rockchip-base-bb98340ac56e.patch
```

## Buildroot 使用

### 打开 Xenomai 配置并编译 rootfs.img

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

### Xenomai 的 Cobalt 补丁

将Xenomai 的 Cobalt 补丁打入内核

```bash
$ cd $sdk/kernel
$ ../buildroot/output/rockchip_rk3588/build/xenomai-v3.2.5/scripts/prepare-kernel.sh --arch=arm64
```

### 编译内核

Linux 6.1 编译命令如下，此处以 RK3588 为例：

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

### 烧录镜像

烧录 `boot.img` 和 `rootfs.img`。

### 测试实时性能

校准 `latency`：

```bash
$ echo 0 > /proc/xenomai/latency
```

使用 `cyclictest` 测试：

```bash
$ ./usr/demo/cyclictest -c 0 -m -n -t -p99 -D12h
```

## Debian / Ubuntu 使用

### Xenomai 的 Cobalt 补丁

在 `$sdk` 目录下下载 Xenomai 源码：

```bash
$ cd $sdk
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

把 Xenomai 的 Cobalt 补丁打到内核上：

```bash
$ cd $sdk/kernel
$ ../xenomai/scripts/prepare-kernel.sh --arch=arm64
```

### 编译内核

Linux 6.1 编译命令如下，此处以 RK3588 为例：

```bash
$ cd $sdk/kernel
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3588_linux.config firefly-linux.config
$ make ARCH=arm64 rk3588-firefly-itx-3588j.img -j8
```

### 烧录镜像

烧录 `boot.img`。

### 安装 Xenomai 到系统

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

### 配置环境变量

```bash
$ export XENOMAI_ROOT_DIR=/usr/xenomai
$ export XENOMAI_PATH=/usr/xenomai
$ export PATH=$PATH:$XENOMAI_PATH/bin:$XENOMAI_PATH/sbin
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:$XENOMAI_PATH/lib/pkgconfig
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$XENOMAI_PATH/lib
$ export OROCOS_TARGET=xenomai
```

### 测试实时性能

校准 `latency`：

```bash
$ echo 0 > /proc/xenomai/latency
```

使用 `latency` 测试：

```bash
$ latency -t0 -p 100 -P 99 -h -g result.txt
```
