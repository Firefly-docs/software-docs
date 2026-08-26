# AMP 调试与排错

## 12. 调试与排错

### 12.1 建议的调试顺序

1. 只启动 RTOS/HAL，确认入口地址、链接地址和调试串口正常。
2. 加入 Linux，先不启用 RPMsg，确认两端都能稳定启动。
3. 检查 `/proc/iomem`、DTS 保留内存和各 ELF 的链接区间。
4. 启用 Mailbox/SoftIRQ 和 RPMsg，先跑 SDK 自带 Demo。
5. 最后转移实际外设和业务代码，每次只转移一类资源。

Linux 侧可用以下命令辅助检查：

```bash
cat /proc/iomem
cat /proc/interrupts
dmesg | grep -Ei 'amp|rpmsg|virtio|mailbox|reserved|failed|error'
ls /sys/firmware/devicetree/base/reserved-memory/
```

对 RTOS/HAL ELF 检查入口和段地址：

```bash
<cross-readelf> -h <firmware>.elf
<cross-readelf> -l <firmware>.elf
<cross-nm> -n <firmware>.elf | head
```

### 12.2 常见问题

| 现象 | 常见原因 | 处理方法 |
| --- | --- | --- |
| U-Boot 没有 `Brought up cpu` 日志 | U-Boot 未开启 `rk-amp.config`、未找到 `amp` 分区或 FIT 校验失败 | 检查 U-Boot config、分区表、`amp.img` 和 `loadables` |
| 从核启动后立即异常 | `load` 与链接地址不一致，或固件超出分配区域 | 对比 ITS、map 文件、ELF program headers 和 DTS |
| Linux 随机崩溃或数据损坏 | 从核内存未在 Linux 中保留，或与 CMA/其他保留区重叠 | 检查 `reserved-memory`、`/proc/iomem` 和实际 DDR 容量 |
| RTOS/HAL 无串口输出 | UART 被 Linux 占用、IOMUX 组错误、时钟未托管或波特率不一致 | 关闭 Linux UART 节点，核对 AMP clocks/pinctrl 和板级 IOMUX |
| 外设轮询可用但中断不响应 | GIC/NVIC/IPIC 路由错误，或未使能外设中断源 | 依次检查中断号、目标 CPU、ISR 和外设寄存器 |
| RPMsg 只显示 `host is online` | Remote 未启动、共享地址不一致或 Name Service 未 announce | 检查 Remote 日志、vring 地址、link ID 和 endpoint name |
| RPMsg 小概率丢包/卡死 | Cache 属性不一致、共享内存重叠或内存屏障缺失 | 优先恢复 SDK 默认 uncached 配置，检查 MMU/MPU 映射 |
| `/dev/ttyRPMSG*` 未出现 | `CONFIG_RPMSG_TTY` 未开启或 Remote 未 announce `rpmsg-tty` | 检查 Kernel config 和 Remote Name Service |
| 新外设在 Linux 和 RTOS 两端都异常 | 外设、pinctrl、reset 或 clock 仍被两端同时管理 | 建立单一所有者，在 Linux DTS 关闭该外设并由 AMP 节点托管必需资源 |

### 12.3 JTAG 调试

- Cortex-A RTOS/HAL 可使用 OpenOCD + JTAG 调试，加载对应 CPU 的 ELF 符号。
- Cortex-M MCU 可使用 J-Link/Ozone 调试，需使用目标 SoC 支持的连接脚本和地址映射。
- 调试器中加载的 ELF 必须与开发板当前运行的 `amp.img` 来自同一次构建。
