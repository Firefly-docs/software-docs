# AMP 中断配置

## 11. 中断配置要点

### 11.1 Cortex-A GIC

GIC 中断分为：

- SGI：中断号 0～15，软件生成，每个 CPU 私有。
- PPI：中断号 16～31，每个 CPU 私有。
- SPI：中断号从 32 开始，由多个 CPU 共享。

AP RTOS/HAL 中需要统一配置外设中断的优先级和目标 CPU：

```c
#define DEFAULT_IRQ_CPU 1

static struct GIC_AMP_IRQ_INIT_CFG irqsConfig[] = {
    GIC_AMP_IRQ_CFG_ROUTE(GPIO0_IRQn, 0xd0,
                          CPU_GET_AFFINITY(0, 0)),
    GIC_AMP_IRQ_CFG_ROUTE(0, 0,
                          CPU_GET_AFFINITY(DEFAULT_IRQ_CPU, 0)),
};
```

没有显式配置的中断会路由到 `DEFAULT_IRQ_CPU`。多个 RTOS/HAL 系统共享一张 GIC 路由表时，必须保证每个 SPI 只有一个明确的处理者。

### 11.2 Cortex-M NVIC 和 RISC-V IPIC

- Cortex-M 使用 NVIC，外部中断超过直连能力时通常使用 INTMUX。
- RK3568 RISC-V 核使用 IPIC，外设中断同样可通过 INTMUX 转发。
- `HAL_Init()` 通常已完成 NVIC 初始化；RISC-V 工程则需确认 `HAL_INTMUX_Init()`/IPIC 初始化路径。

外设中断完整启用流程为：路由到目标核 → 注册 ISR → 使能中断控制器 → 使能外设自身的中断源。
