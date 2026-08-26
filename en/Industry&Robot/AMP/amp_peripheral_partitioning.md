# AMP Peripheral Partitioning

## 8. Peripheral resource partitioning

The Linux DTS describes most SoC peripherals by default. Assigning a peripheral to RTOS/HAL requires transferring the entire ownership of that peripheral, not merely disabling its Linux driver.

For example, use the following sequence to assign I2C1 to a remote core:

1. Disable I2C1 in the Linux board DTS.
2. Add the I2C1 clocks, pinctrl, and interrupt to the `rockchip-amp` node.
3. Configure the same IOMUX, clocks, and interrupt routing in RT-Thread/HAL.
4. Confirm that no other Linux node references the peripheral or its pins.

Disable the device on the Linux side:

```dts
&i2c1 {
    status = "disabled";
};
```

Example AMP node structure:

```dts
rockchip_amp: rockchip-amp {
    compatible = "rockchip,amp";

    /* Append I2C1 clocks to the node's existing clocks list. */
    clocks = <&cru CLK_I2C1>, <&cru PCLK_I2C1>;

    /* Append the I2C1 pinctrl group to the node's existing pin list. */
    pinctrl-names = "default";
    pinctrl-0 = <&i2c1m0_xfer>;

    /* Append the I2C1 interrupt to the node's existing amp-irqs list. */
    amp-irqs = /bits/ 64 <
        GIC_AMP_IRQ_CFG_ROUTE(45, 0xd0, CPU_GET_AFFINITY(3, 0))
    >;

    status = "okay";
};
```

> In the RK3562 I2C1 example, the peripheral interrupt number in the DTS is 13, while the SPI interrupt passed to the GIC AMP configuration is `13 + 32 = 45`. Interrupt numbering rules and macros can differ by SoC and GIC version. Use an existing AMP DTS from the target SDK as the reference.

### 8.1 RT-Thread side

Common RT-Thread files include:

```text
rtos/bsp/rockchip/<target>/board/common/board_base.c
rtos/bsp/rockchip/<target>/board/common/iomux_base.c
rtos/bsp/rockchip/<target>/board/<board>/board.c
rtos/bsp/rockchip/<target>/board/<board>/iomux.c
```

Functions and structures declared with `RT_WEAK` in the common implementation can be redefined by board-specific files. Keep IOMUX and board-specific peripheral setup in the board directory instead of changing the common initialization sequence.

An AP peripheral interrupt must be routed to the current CPU in the GIC configuration table:

```c
#define CUR_CPU 3

static struct GIC_AMP_IRQ_INIT_CFG irqsConfig[] = {
    GIC_AMP_IRQ_CFG_ROUTE(I2C1_IRQn, 0xd0,
                          CPU_GET_AFFINITY(CUR_CPU, 0)),
};
```

Interrupts directly connected to an MCU use NVIC. Other MCU peripheral interrupts are normally forwarded through INTMUX. The RK3568 RISC-V core uses IPIC/INTMUX.

### 8.2 Bare-metal side

RK HAL projects normally perform the following operations in `main.c` or board-specific files:

1. Call `HAL_Init()` and `BSP_Init()`.
2. On an AP, call `HAL_GIC_Init()` to initialize GIC routing.
3. Call `HAL_PINCTRL_SetIOMUX()` to configure pins.
4. Obtain the clock rate and initialize the peripheral.
5. Register the interrupt handler and enable the interrupt.
