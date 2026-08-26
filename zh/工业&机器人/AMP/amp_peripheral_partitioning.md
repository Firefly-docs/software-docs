# AMP 外设资源划分

## 8. 外设资源划分

Linux DTS 默认描述了大部分 SoC 外设。将某个外设交给 RTOS/HAL 时，需要完整转移外设的使用权，而不是只关闭 Linux 驱动。

以将 I2C1 交给从核为例，处理顺序如下：

1. 在 Linux 板级 DTS 中禁用 I2C1。
2. 将 I2C1 的时钟、pinctrl 和中断资源加入 `rockchip-amp` 节点。
3. 在 RT-Thread/HAL 中配置相同的 IOMUX、时钟和中断路由。
4. 确认 Linux 端不再有其他节点引用该外设或它的引脚。

Linux 侧禁用设备：

```dts
&i2c1 {
    status = "disabled";
};
```

AMP 节点的结构示例：

```dts
rockchip_amp: rockchip-amp {
    compatible = "rockchip,amp";

    /* 将 I2C1 时钟追加到节点已有 clocks 列表 */
    clocks = <&cru CLK_I2C1>, <&cru PCLK_I2C1>;

    /* 将 I2C1 pinctrl 追加到节点已有引脚列表 */
    pinctrl-names = "default";
    pinctrl-0 = <&i2c1m0_xfer>;

    /* 将 I2C1 中断追加到节点已有 amp-irqs 列表 */
    amp-irqs = /bits/ 64 <
        GIC_AMP_IRQ_CFG_ROUTE(45, 0xd0, CPU_GET_AFFINITY(3, 0))
    >;

    status = "okay";
};
```

> 在文档的 RK3562 I2C1 示例中，DTS 外设中断号是 13，传给 GIC AMP 配置的 SPI 中断号为 `13 + 32 = 45`。中断编号规则和宏可能因 SoC/GIC 版本而异，应以目标 SDK 的现有 AMP DTS 为参照。

### 8.1 RT-Thread 侧

RT-Thread 常用文件：

```text
rtos/bsp/rockchip/<target>/board/common/board_base.c
rtos/bsp/rockchip/<target>/board/common/iomux_base.c
rtos/bsp/rockchip/<target>/board/<board>/board.c
rtos/bsp/rockchip/<target>/board/<board>/iomux.c
```

通用实现中的 `RT_WEAK` 函数或结构体可以在板级文件中重定义。建议将 IOMUX 和板级外设配置放在板级目录，不要直接修改通用初始化顺序。

AP 核外设中断需要在 GIC 配置表中路由到当前 CPU：

```c
#define CUR_CPU 3

static struct GIC_AMP_IRQ_INIT_CFG irqsConfig[] = {
    GIC_AMP_IRQ_CFG_ROUTE(I2C1_IRQn, 0xd0,
                          CPU_GET_AFFINITY(CUR_CPU, 0)),
};
```

MCU 与其直连的中断使用 NVIC；其他外设中断通常通过 INTMUX 转发。RK3568 RISC-V 核使用 IPIC/INTMUX。

### 8.2 Bare-metal 侧

RK HAL 通常在工程 `main.c` 或板级文件中完成：

1. `HAL_Init()` 和 `BSP_Init()`。
2. AP 侧调用 `HAL_GIC_Init()` 初始化 GIC 路由。
3. 调用 `HAL_PINCTRL_SetIOMUX()` 配置引脚。
4. 获取时钟并初始化外设。
5. 注册中断服务程序并使能中断。
