# AMP 快速上手：编译

## 5. 快速上手

### 5.3 编译

一键编译和打包：

```bash
./build.sh
```

仅重新编译 AMP 固件：

```bash
./build.sh amp
```

分别编译 U-Boot 和 Linux Kernel：

```bash
./build.sh uboot
./build.sh kernel
```

清理后重新编译：

```bash
./build.sh cleanall
./build.sh
```

`./build.sh amp` 会解析 ITS 中的 `compile` 节点，构建对应的 RT-Thread/HAL 工程，然后生成 `amp.img`。可用以下命令确认输出位置：

```bash
find . -name amp.img -printf '%TY-%Tm-%Td %TH:%TM  %p\n' | sort
```

### 5.4 单独编译组件

以 RK3562 为例，Linux Kernel 可单独编译：

```bash
cd <SDK>/kernel
export ARCH=arm64
export CROSS_COMPILE=<SDK>/prebuilts/gcc/linux-x86/aarch64/<toolchain>/bin/aarch64-none-linux-gnu-
make rockchip_linux_defconfig
make <board>-linux-amp.img -j$(nproc)
```

AP RT-Thread：

```bash
cd <SDK>/rtos/bsp/rockchip/<ap-target>/
./build.sh <cpu_id|all>
./mkimage.sh
```

AP Bare-metal：

```bash
cd <SDK>/hal/project/<ap-target>/GCC
./build.sh <cpu_id|all>
cd ..
./mkimage.sh
```

U-Boot：

```bash
cd <SDK>/u-boot
make <soc>_defconfig rk-amp.config
./make.sh
```

> 注：`<board>`、`<ap-target>`、`<soc>` 和工具链路径必须替换为当前 Firefly SDK 中的实际名称。
