# AMP 概述

## 1. 概述

AMP（Asymmetric Multi-Processing，非对称多处理）允许在同一颗 SoC 的不同处理器核上独立运行 Linux、RT-Thread 或 Bare-metal 程序。各系统共享一颗 SoC，但使用独立划分的 CPU、内存、中断和外设资源。

AMP 常用于既需要 Linux 生态，又对硬实时性有要求的场景，例如：

- 工业控制、机器人运动控制和数据采集。
- 电力继电保护和实时信号处理。
- Linux 负责网络、图形、存储和算法，RTOS 或 MCU 负责快速响应。
- 将原本的“主处理器 + 外挂 MCU”方案集成到一颗 SoC 中。

Rockchip AMP SDK 主要提供：

- U-Boot 加载和启动从核固件。
- Linux、RT-Thread 和 RK HAL Bare-metal 开发环境。
- CPU、内存、中断、引脚、时钟和外设的资源划分机制。
- 基于“核间中断 + 共享内存”的 RPMsg/RPMsg-Lite 通信方案。

Rockchip 方案默认使用无监督的 AMP 架构，不依赖 Hypervisor。它能减少虚拟化引入的中断延迟，但也意味着开发者必须自行保证各系统的资源互不冲突。

### 1.1 系统角色

```text
Linux / 先启动的 RTOS（Master Core）
              │
              ├── 分配 CPU、内存和共享外设
              ├── 加载、启动与管理从核
              └── 管理 RPMsg 共享内存
                         │
                         └── RT-Thread / Bare-metal（Remote Core）
```

- Linux + RTOS/Bare-metal：运行 Linux 的核是主核，RTOS/Bare-metal 所在核是从核。
- RTOS + Bare-metal：第一个启动的处理器核是主核。
- AP + MCU：Linux 所在 AP 是主核，MCU 是从核。
