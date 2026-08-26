# AMP SDK Structure

## 3. AMP SDK structure

The directories directly related to heterogeneous development are shown below:

```text
<SDK>/
├── device/rockchip/       # Unified build scripts, board defconfigs, and FIT ITS files
├── kernel/ or kernel-*    # Linux Kernel, AMP management driver, and RPMsg
├── hal/                   # RK HAL bare-metal library and examples
├── rtos/                  # RT-Thread, board configuration, and RPMsg-Lite
├── u-boot/                # AMP FIT loading and remote-core startup
├── rkbin/                 # BL31, Loader, and special boot configurations
├── prebuilts/             # Cross-compilation toolchains
└── tools/                 # Packaging and firmware tools
```

Common files and their purposes:

| File | Purpose |
| --- | --- |
| `device/rockchip/.chip/rockchip_xxx_defconfig` | Selects AMP projects, architecture, ITS, U-Boot configuration, and partition table |
| `device/rockchip/<soc>/amp_linux.its` | Packaging description for Linux + AP RTOS/bare metal |
| `device/rockchip/<soc>/amp.its` | Packaging description for RTOS + bare metal |
| `device/rockchip/<soc>/amp_mcu.its` | Packaging description for Linux + MCU RTOS/bare metal |
| `kernel/arch/arm64/boot/dts/rockchip/*-amp.dts*` | Linux-side reserved memory, interrupts, pins, clocks, and peripheral assignment |
| `kernel/drivers/soc/rockchip/rockchip_amp.c` | AMP resources and remote-core lifecycle management |
| `rtos/bsp/rockchip/<target>/` | RT-Thread board project |
| `hal/project/<target>/` | Bare-metal board project |

Directory names may differ slightly between SDK releases. Use the following commands to locate the actual files:

```bash
find device/rockchip -name 'amp*.its' -o -name '*amp*defconfig'
find kernel* -path '*rockchip*' -name '*amp*.dts*'
```
