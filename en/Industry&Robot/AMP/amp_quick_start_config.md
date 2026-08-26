# AMP Quick Start: Configuration

## 5. Quick start

The following procedure uses the unified SDK build script. Menu entries and target names can differ between releases; use `./build.sh help` as the authoritative reference for the current SDK.

### 5.1 Select the chip and board configuration

Run the following commands from the SDK root directory:

```bash
./build.sh chip
./build.sh lunch
```

Select an AMP-enabled defconfig that exactly matches the Firefly board. Then inspect the selected configuration:

```bash
grep -E '^RK_AMP|^RK_UBOOT_CFG_FRAGMENTS|^RK_PARAMETER' \
  device/rockchip/.chip/rockchip_*_defconfig
```

The important options are:

```text
RK_AMP=y                         # Enable AMP builds
RK_AMP_ARCH="arm"               # Use AArch32 for AP RTOS/HAL; use arm64 for 64-bit
RK_AMP_HAL_TARGET="<target>"     # AP bare-metal project
RK_AMP_RTT_TARGET="<target>"     # AP RT-Thread project
RK_AMP_MCU_HAL_TARGET="<target>" # MCU bare-metal project
RK_AMP_MCU_RTT_TARGET="<target>" # MCU RT-Thread project
RK_AMP_FIT_ITS="amp_linux.its"   # AMP FIT packaging configuration
RK_UBOOT_CFG_FRAGMENTS="rk-amp"  # Enable AMP in U-Boot
RK_PARAMETER="parameter.txt"     # Partition table
```

If the SDK provides a `menuconfig` interface, prefer it over manually editing configuration files so that dependencies are preserved.

### 5.2 Select an AMP combination

| Target combination | ITS file | Description |
| --- | --- | --- |
| Linux + AP RT-Thread/HAL | `amp_linux.its` | Linux runs on the master core and one or more AP cores run an RTOS/HAL |
| RT-Thread + bare metal | `amp.its` | AP cores run RTOS/HAL as required, without Linux |
| Linux + MCU RT-Thread/HAL | `amp_mcu.its` | The AP runs Linux and the SoC's internal MCU runs an RTOS/HAL |

Verify the `cpu`, `load`, `size`, `sys`, and `loadables` properties for every remote-core image in the ITS file. Never change only `load` without updating the Linux DTS and RTOS/HAL linker configuration.
