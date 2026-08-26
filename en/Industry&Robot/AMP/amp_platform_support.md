# AMP Platform Support

## 2. Platform support

| SoC | Processor cores | Linux | RTOS | Bare metal |
| --- | --- | --- | --- | --- |
| RK3588 | 4 × Cortex-A76 | Kernel 5.10 | Not supported | Not supported |
| RK3588 | 4 × Cortex-A55 | Kernel 5.10 | RT-Thread 3.1/4.1, 32-bit | HAL, 32-bit |
| RK3588 | 1 × Cortex-M0 | Not supported | RT-Thread 3.1/4.1 | HAL |
| RK3576 | 4 × Cortex-A72 | Kernel 6.1 | Not supported | Not supported |
| RK3576 | 4 × Cortex-A53 | Kernel 6.1 | RT-Thread 4.1, 32-bit | HAL, 32-bit |
| RK3576 | 1 × Cortex-M0 | Not supported | RT-Thread 4.1 | HAL |
| RK3568 | 4 × Cortex-A55 | Kernel 4.19/5.10 | RT-Thread 3.1, 32-bit | HAL, 32-bit |
| RK3568 | 1 × RISC-V | Not supported | RT-Thread 3.1 | HAL |
| RK3562 | 4 × Cortex-A53 | Kernel 5.10 | RT-Thread 4.1, 32-bit | HAL, 32-bit |
| RK3562 | 1 × Cortex-M0 | Not supported | RT-Thread 4.1 | HAL |
| RK3358 | 4 × Cortex-A35 | Not supported | RT-Thread 3.1, 32-bit | HAL, 32-bit |
| RK3308 | 4 × Cortex-A35 | Kernel 5.10 | RT-Thread 3.1/4.1, 32-bit | HAL, 32-bit |

> Note: "32-bit" in this table means that the RTOS/HAL firmware on an AP runs in AArch32 state. Linux can continue to run in AArch64 state.
