# AMP 快速上手：配置

## 5. 快速上手

以下流程使用 SDK 统一构建脚本。不同 SDK 的菜单和目标名可能不同，请以 `./build.sh help` 的输出为准。

### 5.1 选择芯片和板级配置

在 SDK 根目录执行：

```bash
./build.sh chip
./build.sh lunch
```

选择与 Firefly 板卡完全匹配、且已启用 AMP 的 defconfig。然后检查当前配置：

```bash
grep -E '^RK_AMP|^RK_UBOOT_CFG_FRAGMENTS|^RK_PARAMETER' \
  device/rockchip/.chip/rockchip_*_defconfig
```

关键项的含义如下：

```text
RK_AMP=y                         # 启用 AMP 构建
RK_AMP_ARCH="arm"               # AP RTOS/HAL 使用 AArch32；64 位时为 arm64
RK_AMP_HAL_TARGET="<target>"     # AP Bare-metal 工程
RK_AMP_RTT_TARGET="<target>"     # AP RT-Thread 工程
RK_AMP_MCU_HAL_TARGET="<target>" # MCU Bare-metal 工程
RK_AMP_MCU_RTT_TARGET="<target>" # MCU RT-Thread 工程
RK_AMP_FIT_ITS="amp_linux.its"   # AMP FIT 打包配置
RK_UBOOT_CFG_FRAGMENTS="rk-amp"  # U-Boot 开启 AMP
RK_PARAMETER="parameter.txt"     # 分区表
```

如果 SDK 提供 `menuconfig`，优先通过配置菜单修改，以避免破坏配置依赖。

### 5.2 选择 AMP 组合

| 目标组合 | ITS 文件 | 说明 |
| --- | --- | --- |
| Linux + AP RT-Thread/HAL | `amp_linux.its` | Linux 运行在主核，一个或多个 AP 核运行 RTOS/HAL |
| RT-Thread + Bare-metal | `amp.its` | AP 核按需运行 RTOS/HAL，不启动 Linux |
| Linux + MCU RT-Thread/HAL | `amp_mcu.its` | AP 运行 Linux，SoC 内置 MCU 运行 RTOS/HAL |

在 ITS 中确认从核固件的 `cpu`、`load`、`size`、`sys` 和 `loadables`。不要只修改 `load` 而不同步修改 Linux DTS 和 RTOS/HAL 链接配置。
