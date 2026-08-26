# AMP Prerequisites

## 4. Prerequisites

### 4.1 Hardware and software

- A Firefly board that supports AMP.
- A Firefly/Rockchip AMP SDK matching the board and SoC.
- The build dependencies and cross-compilation toolchain required by the SDK.
- A serial adapter. It is recommended to connect both the Linux debug UART and the RTOS/HAL debug UART.
- A recoverable full firmware image in case an invalid memory or boot configuration prevents normal startup.

The default AMP debug UART parameters are usually:

| Baud rate | Data bits | Stop bits | Parity | Flow control |
| --- | --- | --- | --- | --- |
| 1500000 | 8 | 1 | none | none |

> The actual debug UART and IOMUX configuration depend on the target board. Do not directly reuse UART pins from another board.

### 4.2 Plan resources first

Create a resource allocation table before building the firmware:

| Resource | Linux | Remote Core | Checks |
| --- | --- | --- | --- |
| CPU | CPU0 through CPUn | Specified AP core or MCU | An AP core cannot belong to both Linux SMP and an RTOS |
| Private memory | Linux DRAM | RTOS/HAL firmware region | DTS `reserved-memory`, ITS `load/size`, and the linker script must agree |
| Shared memory | RPMsg/shared memory | Same physical region | Must not overlap CMA, display buffers, NPU memory, or another reserved region |
| Peripheral | Linux driver | RTOS/HAL driver | Only one system may actively manage a device |
| Interrupt | Linux GIC | GIC/NVIC/IPIC | Core mapping, interrupt number, and routing must agree |
| Pin | Linux pinctrl | HAL IOMUX | Check the pinmux group and electrical settings |
| Clock/power domain | Managed by the Linux AMP driver | Used by RTOS/HAL | Linux must not disable the clock or power domain because it sees no consumer |
