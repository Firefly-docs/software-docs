# AMP 使用指南

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

## 3. AMP SDK 结构

AMP SDK 中与异构开发直接相关的目录如下：

```text
<SDK>/
├── device/rockchip/       # 统一构建脚本、板级 defconfig 和 FIT ITS
├── kernel/ 或 kernel-*     # Linux Kernel、AMP 管理驱动和 RPMsg
├── hal/                   # RK HAL Bare-metal 库与示例
├── rtos/                  # RT-Thread、板级配置和 RPMsg-Lite
├── u-boot/                # AMP FIT 加载和从核启动
├── rkbin/                 # BL31、Loader 及特殊启动配置
├── prebuilts/             # 交叉编译工具链
└── tools/                 # 打包和固件工具
```

常见文件的作用：

| 文件 | 作用 |
| --- | --- |
| `device/rockchip/.chip/rockchip_xxx_defconfig` | 选择 AMP 工程、架构、ITS、U-Boot 配置和分区表 |
| `device/rockchip/<soc>/amp_linux.its` | Linux + AP RTOS/Bare-metal 打包描述 |
| `device/rockchip/<soc>/amp.its` | RTOS + Bare-metal 打包描述 |
| `device/rockchip/<soc>/amp_mcu.its` | Linux + MCU RTOS/Bare-metal 打包描述 |
| `kernel/arch/arm64/boot/dts/rockchip/*-amp.dts*` | Linux 侧保留内存、中断、引脚、时钟和外设划分 |
| `kernel/drivers/soc/rockchip/rockchip_amp.c` | AMP 资源和从核生命周期管理 |
| `rtos/bsp/rockchip/<target>/` | RT-Thread 板级工程 |
| `hal/project/<target>/` | Bare-metal 板级工程 |

SDK 版本不同时，目录名称可能略有变化。可使用以下命令查找实际文件：

```bash
find device/rockchip -name 'amp*.its' -o -name '*amp*defconfig'
find kernel* -path '*rockchip*' -name '*amp*.dts*'
```

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

## 5. 快速上手

以下流程使用 SDK 统一构建脚本。不同 SDK 的菜单和目标名可能不同，请以 `./build.sh help` 的输出为准。

### 5.1 选择芯片和板级配置

在 SDK 根目录执行：

```bash
./build.sh chip
./build.sh lunch
```

选择与 Firefly 板卡完全匹配、且已启用 AMP 的 defconfig。然后检查当前配置：

```bash
grep -E '^RK_AMP|^RK_UBOOT_CFG_FRAGMENTS|^RK_PARAMETER' \
  device/rockchip/.chip/rockchip_*_defconfig
```

关键项的含义如下：

```text
RK_AMP=y                         # 启用 AMP 构建
RK_AMP_ARCH="arm"               # AP RTOS/HAL 使用 AArch32；64 位时为 arm64
RK_AMP_HAL_TARGET="<target>"     # AP Bare-metal 工程
RK_AMP_RTT_TARGET="<target>"     # AP RT-Thread 工程
RK_AMP_MCU_HAL_TARGET="<target>" # MCU Bare-metal 工程
RK_AMP_MCU_RTT_TARGET="<target>" # MCU RT-Thread 工程
RK_AMP_FIT_ITS="amp_linux.its"   # AMP FIT 打包配置
RK_UBOOT_CFG_FRAGMENTS="rk-amp"  # U-Boot 开启 AMP
RK_PARAMETER="parameter.txt"     # 分区表
```

如果 SDK 提供 `menuconfig`，优先通过配置菜单修改，以避免破坏配置依赖。

### 5.2 选择 AMP 组合

| 目标组合 | ITS 文件 | 说明 |
| --- | --- | --- |
| Linux + AP RT-Thread/HAL | `amp_linux.its` | Linux 运行在主核，一个或多个 AP 核运行 RTOS/HAL |
| RT-Thread + Bare-metal | `amp.its` | AP 核按需运行 RTOS/HAL，不启动 Linux |
| Linux + MCU RT-Thread/HAL | `amp_mcu.its` | AP 运行 Linux，SoC 内置 MCU 运行 RTOS/HAL |

在 ITS 中确认从核固件的 `cpu`、`load`、`size`、`sys` 和 `loadables`。不要只修改 `load` 而不同步修改 Linux DTS 和 RTOS/HAL 链接配置。

### 5.3 编译

一键编译和打包：

```bash
./build.sh
```

仅重新编译 AMP 固件：

```bash
./build.sh amp
```

分别编译 U-Boot 和 Linux Kernel：

```bash
./build.sh uboot
./build.sh kernel
```

清理后重新编译：

```bash
./build.sh cleanall
./build.sh
```

`./build.sh amp` 会解析 ITS 中的 `compile` 节点，构建对应的 RT-Thread/HAL 工程，然后生成 `amp.img`。可用以下命令确认输出位置：

```bash
find . -name amp.img -printf '%TY-%Tm-%Td %TH:%TM  %p\n' | sort
```

### 5.4 单独编译组件

以 RK3562 为例，Linux Kernel 可单独编译：

```bash
cd <SDK>/kernel
export ARCH=arm64
export CROSS_COMPILE=<SDK>/prebuilts/gcc/linux-x86/aarch64/<toolchain>/bin/aarch64-none-linux-gnu-
make rockchip_linux_defconfig
make <board>-linux-amp.img -j$(nproc)
```

AP RT-Thread：

```bash
cd <SDK>/rtos/bsp/rockchip/<ap-target>/
./build.sh <cpu_id|all>
./mkimage.sh
```

AP Bare-metal：

```bash
cd <SDK>/hal/project/<ap-target>/GCC
./build.sh <cpu_id|all>
cd ..
./mkimage.sh
```

U-Boot：

```bash
cd <SDK>/u-boot
make <soc>_defconfig rk-amp.config
./make.sh
```

> 注：`<board>`、`<ap-target>`、`<soc>` 和工具链路径必须替换为当前 Firefly SDK 中的实际名称。

### 5.5 配置 AMP 分区

使用 eMMC 或 SPI Flash 启动时，分区表中需要有容纳 `amp.img` 的 `amp` 分区。先查看当前分区：

```bash
./build.sh list-parts
```

例如，在第 4 个位置插入一个 2 MiB 的 AMP 分区：

```bash
./build.sh insert-part:4:amp:2M
./build.sh list-parts
```

分区大小必须大于实际 `amp.img`，并考虑后续功能增长。修改分区表后通常需要同步更新 `parameter.txt` 或重新生成完整固件。

### 5.6 烧录与启动验证

按 Firefly 板卡对应的固件升级方法烧录：

- 更改了分区表时，建议烧录新生成的完整固件。
- 分区表未变更时，可按开发板升级文档单独更新 `uboot.img`、`boot.img` 和 `amp.img`。
- 使用特殊 BL31/Loader 的方案，还必须同步更新相应引导固件。

启动时先检查 U-Boot 是否成功释放从核。以 RK3562 CPU3 为例：

```text
AMP: Brought up cpu[3] with state 0x10, entry 0x01800000 ...OK
```

Bare-metal 从核的典型输出：

```text
Hello RK3562 Bare-metal using RK_HAL!
CPI_ID(3)
CPU(3) Initial OK!
```

RT-Thread 从核应输出 RT-Thread 版本信息并进入 shell 或主应用。

## 6. FIT 打包配置

AMP 固件使用 U-Boot FIT 格式打包。下面是一个简化的 Linux + CPU3 RT-Thread 示例：

```dts
/dts-v1/;

/ {
    description = "Rockchip AMP FIT Image";
    #address-cells = <1>;

    images {
        amp3 {
            description = "rtt-core3";
            data = /incbin/("rtt3.bin");
            type = "firmware";
            compression = "none";
            arch = "arm";
            cpu = <3>;
            load = <0x01800000>;

            compile {
                size = <0x00800000>;
                sys = "rtt";
                core = "ap";
                rtt_config = "board/<board>/amp_defconfig";
            };

            udelay = <10000>;
            hash {
                algo = "sha256";
            };
        };
    };

    share {
        shm_base = <0x07800000>;
        shm_size = <0x00400000>;
        rpmsg_base = <0x07c00000>;
        rpmsg_size = <0x00500000>;
    };

    configurations {
        default = "conf";
        conf {
            loadables = "amp3";
            linux {
                arch = "arm64";
                cpu = <0>;
                load = <0x02000000>;
                load_c = <0x04880000>;
            };
        };
    };
};
```

> 重要：上述地址仅用于解释 RK3562 示例，不是 Firefly 所有板卡的通用地址。必须以目标 SDK 内已验证的 ITS 和 DTS 为起点。

### 6.1 关键属性

| 属性 | 说明 |
| --- | --- |
| `data` | 需要打包的 RTOS/HAL 二进制文件 |
| `type` | AP 固件通常为 `firmware`，MCU 固件通常为 `standalone` |
| `arch` | 从核固件的指令集，例如 `arm` 或 `arm64` |
| `cpu` | AP 的硬件 CPU ID |
| `load` | 固件的物理加载和运行地址 |
| `size` | 该从核的私有运行内存大小 |
| `sys` | `rtt` 或 `hal` |
| `core` | `ap` 或 `mcu` |
| `udelay` | 启动下一个核前的延时，单位为微秒 |
| `loadables` | 需要加载的镜像和顺序 |
| `shm_*` | 普通共享内存起始地址和大小 |
| `rpmsg_*` | RPMsg 共享内存起始地址和大小 |

多个从核时，每个核使用独立的 `images` 子节点。各节点的私有内存不得重叠，`loadables` 中的名称必须与节点名一致。

## 7. 内存资源划分

内存冲突是 AMP 系统最常见、也最容易造成随机崩溃的问题。一段从核私有内存至少要同时出现在以下三处：

1. ITS 的 `load` 和 `compile/size`。
2. RT-Thread/HAL 链接脚本或构建脚本的固件起始地址和大小。
3. Linux DTS 的 `reserved-memory`。

### 7.1 Linux 保留从核内存

以从核运行区 `0x01800000～0x01ffffff` 为例：

```dts
/ {
    reserved-memory {
        #address-cells = <2>;
        #size-cells = <2>;
        ranges;

        amp_reserved: amp@1800000 {
            reg = <0x0 0x01800000 0x0 0x00800000>;
            no-map;
        };
    };
};
```

Linux 不会将 `no-map` 内存加入普通页分配器，从而避免覆盖从核代码和数据。

### 7.2 保留 RPMsg 共享内存

Rockchip 示例通常为 RPMsg 划分 5 MiB：4 MiB vring 区和 1 MiB vdev buffer/DMA 区。示例：

```dts
reserved-memory {
    rpmsg_reserved: rpmsg@7c00000 {
        reg = <0x0 0x07c00000 0x0 0x00400000>;
        no-map;
    };

    rpmsg_dma_reserved: rpmsg-dma@8000000 {
        compatible = "shared-dma-pool";
        reg = <0x0 0x08000000 0x0 0x00100000>;
        no-map;
    };
};
```

当前 Rockchip Linux RPMsg 方案默认使用 uncached 共享内存。如果自行改为 cacheable，必须在两端实现正确的 Cache clean/invalidate 和内存屏障，否则会出现丢包、旧数据或 vring 损坏。

### 7.3 AP 与 MCU 地址差异

AP 链接脚本中的地址通常就是 DDR 物理地址。MCU 常以自身加载地址作为本地 `0x0`，因此 MCU 地址与真实物理地址之间存在偏移。

例如，MCU 加载到 `0x08200000`，共享区在 MCU 链接脚本中的偏移为 `0x00100000`，则其真实物理地址是：

```text
0x08200000 + 0x00100000 = 0x08300000
```

修改 MCU 加载地址或 RPMsg 共享区时，需要同时核对：

- `amp_mcu.its` 的 `load`。
- MCU 链接脚本。
- U-Boot 中的 MCU 内存映射和 uncached 区间。
- `rpmsg_platform.c` 中的物理地址/虚拟地址转换。
- Linux DTS 的 `reserved-memory`。

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

## 9. RPMsg 核间通信

Rockchip AMP 通信的底层模型是：

```text
发送端写入共享内存
          │
          ├── vring 更新队列状态
          └── Mailbox / SoftIRQ / SGI 触发核间中断
                                      │
                                      └── 接收端取出并处理消息
```

Linux 侧使用内核 RPMsg/VirtIO，RT-Thread 和 Bare-metal 侧使用 RPMsg-Lite。RPMsg 由两个单向 vring 和 vdev buffer 组成，共享内存的管理者是 Master Core。

SDK 默认 RPMsg buffer 长度为 512 Byte，扣除 16 Byte RPMsg 头部后，单条消息的最大 payload 为 496 Byte。大数据应在应用层分片，或改用共享大缓冲区 + RPMsg 控制消息的方式。

### 9.1 Linux + RT-Thread 配置

Linux Kernel 配置：

```text
CONFIG_MAILBOX=y
CONFIG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_VIRTIO=y
CONFIG_RPMSG_TTY=y                 # 可选，生成 RPMsg TTY
CONFIG_RPMSG_ROCKCHIP_TEST=y       # 可选，开启测试驱动
```

RT-Thread 在 `scons --menuconfig` 中开启：

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_LINUX_RPMSG=y
CONFIG_RT_USING_COMMON_TEST_LINUX_RPMSG_LITE=y  # 可选，测试 Demo
```

### 9.2 Linux + Bare-metal 配置

Linux 侧同样开启 Mailbox、Rockchip RPMsg 和 VirtIO。HAL 侧在对应测试工程中开启：

```c
#define RPMSG_LINUX_TEST
```

实际宏所在文件可能随 SDK 版本变化，可搜索：

```bash
grep -R "RPMSG_LINUX_TEST" -n hal/project
```

### 9.3 RT-Thread + Bare-metal 配置

RT-Thread 侧开启：

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_COMMON_TEST_RPMSG_LITE=y
```

HAL 测试工程一般需开启：

```c
#define TEST_DEMO
#define TEST_USE_RPMSG_INIT
#define RPMSG_TEST
```

### 9.4 验证 Linux + Remote RPMsg

Linux 启动后检查：

```bash
dmesg | grep -Ei 'rpmsg|virtio|mailbox'
ls -l /sys/bus/rpmsg/devices/
ls -l /dev/ttyRPMSG* 2>/dev/null
```

正常初始化时可看到类似日志：

```text
rockchip-rpmsg ...: rockchip rpmsg platform probe
virtio_rpmsg_bus virtio0: rpmsg host is online
virtio_rpmsg_bus virtio0: creating channel rpmsg-ap3-ch0 ...
```

Remote 端的典型日志：

```text
rpmsg remote: remote core cpu_id-3
rpmsg remote: shmem_base-0x7c00000 shmem_end-8100000
rpmsg remote: link up! link_id-0x3
```

Linux 驱动和 Remote 端使用的 Name Service 名称必须一致。例如 Remote 端 announce `rpmsg-tty` 后，Linux `rpmsg_tty` 驱动才会创建 `/dev/ttyRPMSG*`。

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
