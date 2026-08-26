# 在 Debian 或 Ubuntu 中使用 Xenomai

## Xenomai

### Debian / Ubuntu

#### 添加 Xenomai 到内核

在 `$sdk` 目录下下载 Xenomai 源码：

```bash
$ cd $sdk
$ git clone https://source.denx.de/Xenomai/xenomai.git
$ cd xenomai
$ git checkout v3.2.5
```

把 Xenomai 系统打到内核上：

```bash
$ cd $sdk/kernel
$ ../xenomai/scripts/prepare-kernel.sh --arch=arm64
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

烧录 `boot.img`。

#### 安装 Xenomai 到系统

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

#### 配置环境变量

```bash
$ export XENOMAI_ROOT_DIR=/usr/xenomai
$ export XENOMAI_PATH=/usr/xenomai
$ export PATH=$PATH:$XENOMAI_PATH/bin:$XENOMAI_PATH/sbin
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:$XENOMAI_PATH/lib/pkgconfig
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$XENOMAI_PATH/lib
$ export OROCOS_TARGET=xenomai
```

#### 测试实时性能

校准 `latency`：

```bash
$ echo 0 > /proc/xenomai/latency
```

使用 `latency` 测试：

```bash
$ latency -t0 -p 100 -P 99 -h -g result.txt
```
