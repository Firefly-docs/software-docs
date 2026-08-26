# AMP 开发建议与术语

## 13. 开发建议

- 从 SDK 已有的 AMP 板级配置复制，不要从空白 DTS/ITS 开始。
- 先保持默认内存地址和 RPMsg 配置跑通 Demo，再根据产品需求调整。
- 为 Linux、每个 Remote Core 和共享内存维护一张统一的地址分配表。
- 将外设的 clock、reset、pinctrl、power-domain 和 IRQ 视为一个整体转移。
- RPMsg 用于控制和中小消息；高带宽数据建议使用专用共享缓冲区。
- 从核固件应实现独立的 watchdog、超时、错误状态和安全降级机制。
- 量产前应完成多轮冷启动、热重启、高负载、长时间 RPMsg 和外设中断压力测试。

## 14. 术语

| 术语 | 说明 |
| --- | --- |
| AMP | Asymmetric Multi-Processing，非对称多处理 |
| SMP | Symmetric Multi-Processing，对称多处理 |
| AP | Application Processor，应用处理器，通常为 Cortex-A |
| MCU | Microcontroller Unit，微控制器，例如 Cortex-M 或 RISC-V 核 |
| RTOS | Real-Time Operating System，实时操作系统 |
| RTT | 本文中指 RT-Thread |
| HAL | Hardware Abstraction Layer，硬件抽象层 |
| FIT/ITS | U-Boot Flattened Image Tree 及其源描述文件 |
| RPMsg | Remote Processor Messaging，核间消息协议 |
| RPMsg-Lite | 面向 RTOS/Bare-metal 的轻量 RPMsg 实现 |
| vring | VirtIO 使用的环形队列 |
| Master Core | 管理共享资源和通信内存的主核 |
| Remote Core | 由主核加载或管理的从核 |
