# AMP 平台支持

## 2. 平台支持

| SoC | 处理器核 | Linux | RTOS | Bare-metal |
| --- | --- | --- | --- | --- |
| RK3588 | 4 × Cortex-A76 | Kernel 5.10 | 不支持 | 不支持 |
| RK3588 | 4 × Cortex-A55 | Kernel 5.10 | RT-Thread 3.1/4.1 32 位 | HAL 32 位 |
| RK3588 | 1 × Cortex-M0 | 不支持 | RT-Thread 3.1/4.1 | HAL |
| RK3576 | 4 × Cortex-A72 | Kernel 6.1 | 不支持 | 不支持 |
| RK3576 | 4 × Cortex-A53 | Kernel 6.1 | RT-Thread 4.1 32 位 | HAL 32 位 |
| RK3576 | 1 × Cortex-M0 | 不支持 | RT-Thread 4.1 | HAL |
| RK3568 | 4 × Cortex-A55 | Kernel 4.19/5.10 | RT-Thread 3.1 32 位 | HAL 32 位 |
| RK3568 | 1 × RISC-V | 不支持 | RT-Thread 3.1 | HAL |
| RK3562 | 4 × Cortex-A53 | Kernel 5.10 | RT-Thread 4.1 32 位 | HAL 32 位 |
| RK3562 | 1 × Cortex-M0 | 不支持 | RT-Thread 4.1 | HAL |
| RK3358 | 4 × Cortex-A35 | 不支持 | RT-Thread 3.1 32 位 | HAL 32 位 |
| RK3308 | 4 × Cortex-A35 | Kernel 5.10 | RT-Thread 3.1/4.1 32 位 | HAL 32 位 |

> 注：表中的“32 位”是指 AP 上的 RTOS/HAL 固件运行在 AArch32 状态，不影响 Linux 继续使用 AArch64。
