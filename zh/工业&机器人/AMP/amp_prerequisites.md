# AMP 使用前准备

## 4. 使用前准备

### 4.1 硬件和软件

- 一块支持 AMP 的 Firefly 开发板。
- 与板卡和 SoC 匹配的 Firefly/Rockchip AMP SDK。
- 已安装 SDK 要求的构建依赖和交叉编译工具链。
- 串口调试器，建议同时接出 Linux 和 RTOS/HAL 使用的调试串口。
- 可恢复的完整固件，以便在内存或启动配置错误时恢复设备。

AMP 默认调试串口参数通常为：

| 波特率 | 数据位 | 停止位 | 奇偶校验 | 流控 |
| --- | --- | --- | --- | --- |
| 1500000 | 8 | 1 | none | none |

> 具体调试串口和 IOMUX 以目标板级配置为准，不要直接套用其他开发板的串口引脚。

### 4.2 先完成资源规划

开始编译前，建议先建立一张资源表：

| 资源 | Linux | Remote Core | 检查要点 |
| --- | --- | --- | --- |
| CPU | CPU0～CPUn | 指定 AP 核或 MCU | 同一 AP 核不能同时加入 Linux SMP 和 RTOS |
| 私有内存 | Linux DRAM | RTOS/HAL 固件区 | DTS `reserved-memory`、ITS `load/size` 和链接脚本必须一致 |
| 共享内存 | RPMsg/Shared Memory | 相同物理区域 | 不能与 CMA、显存、NPU 或其他保留区重叠 |
| 外设 | Linux 驱动 | RTOS/HAL 驱动 | 只能有一个系统主动管理设备 |
| 中断 | Linux GIC | GIC/NVIC/IPIC | 核对应关系、中断号和路由必须一致 |
| 引脚 | Linux pinctrl | HAL IOMUX | 核对 pinmux 组别和电气属性 |
| 时钟/电源域 | Linux AMP 驱动托管 | RTOS/HAL 使用 | Linux 不得因无消费者而关闭其时钟或电源 |
