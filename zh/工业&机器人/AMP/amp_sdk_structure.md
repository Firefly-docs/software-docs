# AMP SDK 结构

## 3. AMP SDK 结构

AMP SDK 中与异构开发直接相关的目录如下：

```text
<SDK>/
├── device/rockchip/       # 统一构建脚本、板级 defconfig 和 FIT ITS
├── kernel/ 或 kernel-*     # Linux Kernel、AMP 管理驱动和 RPMsg
├── hal/                   # RK HAL Bare-metal 库与示例
├── rtos/                  # RT-Thread、板级配置和 RPMsg-Lite
├── u-boot/                # AMP FIT 加载和从核启动
├── rkbin/                 # BL31、Loader 及特殊启动配置
├── prebuilts/             # 交叉编译工具链
└── tools/                 # 打包和固件工具
```

常见文件的作用：

| 文件 | 作用 |
| --- | --- |
| `device/rockchip/.chip/rockchip_xxx_defconfig` | 选择 AMP 工程、架构、ITS、U-Boot 配置和分区表 |
| `device/rockchip/<soc>/amp_linux.its` | Linux + AP RTOS/Bare-metal 打包描述 |
| `device/rockchip/<soc>/amp.its` | RTOS + Bare-metal 打包描述 |
| `device/rockchip/<soc>/amp_mcu.its` | Linux + MCU RTOS/Bare-metal 打包描述 |
| `kernel/arch/arm64/boot/dts/rockchip/*-amp.dts*` | Linux 侧保留内存、中断、引脚、时钟和外设划分 |
| `kernel/drivers/soc/rockchip/rockchip_amp.c` | AMP 资源和从核生命周期管理 |
| `rtos/bsp/rockchip/<target>/` | RT-Thread 板级工程 |
| `hal/project/<target>/` | Bare-metal 板级工程 |

SDK 版本不同时，目录名称可能略有变化。可使用以下命令查找实际文件：

```bash
find device/rockchip -name 'amp*.its' -o -name '*amp*defconfig'
find kernel* -path '*rockchip*' -name '*amp*.dts*'
```
