# AMP Quick Start: Build

## 5. Quick start

### 5.3 Build

Build and package the complete SDK:

```bash
./build.sh
```

Rebuild only the AMP firmware:

```bash
./build.sh amp
```

Build U-Boot and the Linux Kernel separately:

```bash
./build.sh uboot
./build.sh kernel
```

Clean and rebuild everything:

```bash
./build.sh cleanall
./build.sh
```

`./build.sh amp` parses the `compile` nodes in the ITS file, builds the corresponding RT-Thread/HAL projects, and generates `amp.img`. Locate the output with:

```bash
find . -name amp.img -printf '%TY-%Tm-%Td %TH:%TM  %p\n' | sort
```

### 5.4 Build individual components

The following RK3562 example builds the Linux Kernel separately:

```bash
cd <SDK>/kernel
export ARCH=arm64
export CROSS_COMPILE=<SDK>/prebuilts/gcc/linux-x86/aarch64/<toolchain>/bin/aarch64-none-linux-gnu-
make rockchip_linux_defconfig
make <board>-linux-amp.img -j$(nproc)
```

AP RT-Thread:

```bash
cd <SDK>/rtos/bsp/rockchip/<ap-target>/
./build.sh <cpu_id|all>
./mkimage.sh
```

AP bare metal:

```bash
cd <SDK>/hal/project/<ap-target>/GCC
./build.sh <cpu_id|all>
cd ..
./mkimage.sh
```

U-Boot:

```bash
cd <SDK>/u-boot
make <soc>_defconfig rk-amp.config
./make.sh
```

> Replace `<board>`, `<ap-target>`, `<soc>`, and the toolchain path with the names used by the current Firefly SDK.
