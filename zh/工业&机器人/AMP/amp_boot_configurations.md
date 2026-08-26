# AMP 启动方案

## 10. 启动方案

### 10.1 Linux + AP RTOS/Bare-metal

U-Boot 在 CPU0 上运行，从 `amp.img` 加载指定从核固件，释放对应 CPU，然后继续启动 Linux。Linux 只启动分配给 SMP 的其余 CPU。

例如，四核 SoC 中 CPU3 运行 RT-Thread：

```text
BootROM/Loader → U-Boot@CPU0 → RT-Thread@CPU3
                         └──→ Linux@CPU0/CPU1/CPU2
```

### 10.2 Linux + MCU RTOS/Bare-metal

U-Boot 先加载并释放 MCU，然后继续启动 Linux AP。MCU 节点的 `type` 必须与平台 U-Boot 实现匹配，Rockchip 示例中通常为 `standalone`。

### 10.3 RTOS + Bare-metal

U-Boot 按 `loadables` 加载各 CPU 的固件。当 CPU0 是引导核时，通常先释放其他 CPU，最后由 U-Boot 跳转到 CPU0 固件。

### 10.4 快速启动

部分 SoC 支持在 SPL 阶段提前加载 MCU，或使用 SPI NOR + eMMC 双存储方案缩短 MCU 固件启动时间。该方案需要同时调整 `rkbin` 和 U-Boot，必须使用目标 SoC 专用配置，不建议跨平台复用。
