# AMP Boot Configurations

## 10. Boot configurations

### 10.1 Linux + AP RTOS/bare metal

U-Boot runs on CPU0, loads the specified remote-core firmware from `amp.img`, releases the target CPU, and then continues to boot Linux. Linux starts only the CPUs assigned to its SMP domain.

For example, on a four-core SoC where CPU3 runs RT-Thread:

```text
BootROM/Loader → U-Boot@CPU0 → RT-Thread@CPU3
                         └──→ Linux@CPU0/CPU1/CPU2
```

### 10.2 Linux + MCU RTOS/bare metal

U-Boot loads and releases the MCU before continuing to boot Linux on the AP. The MCU node `type` must match the platform U-Boot implementation. Rockchip examples normally use `standalone`.

### 10.3 RTOS + bare metal

U-Boot loads the firmware for each CPU according to `loadables`. When CPU0 is the boot core, U-Boot normally releases the other CPUs first and finally jumps to the CPU0 firmware.

### 10.4 Fast boot

Some SoCs can load MCU firmware earlier in the SPL stage, or use an SPI NOR + eMMC dual-storage design to reduce MCU startup time. These solutions require coordinated changes to `rkbin` and U-Boot and must use a configuration designed for the target SoC. Do not reuse such configurations across platforms.
