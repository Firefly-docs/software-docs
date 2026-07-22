# EtherCAT IgH 主站使用与平台支持

## 1. 概述

### 1.1 EtherCAT 简介

EtherCAT（Ethernet for Control Automation Technology）是一种面向工业自动化的实时以太网技术。它使用标准以太网物理层，但从站通常在报文经过时直接读取输出数据并写入输入数据，从而减少逐站转发带来的延迟。

EtherCAT 常用于以下场景：

- 伺服驱动器和多轴运动控制。
- 工业 I/O、传感器和执行器。
- 机器人、数控设备和自动化产线。
- 需要分布式时钟同步的测量和控制系统。

典型拓扑如下：

```text
Linux EtherCAT 主站
        │
        ├── 从站 0：伺服驱动器 / I/O
        │
        ├── 从站 1：伺服驱动器 / I/O
        │
        └── 从站 N：传感器 / 执行器
```

EtherCAT 网络应使用独立网口。该网口由主站直接收发二层 EtherCAT 帧，一般不配置 IP 地址，也不应同时由 NetworkManager、DHCP 客户端或普通网络业务管理。

### 1.2 IgH EtherCAT Master 简介

IgH EtherCAT Master 是 EtherLab 项目提供的开源 Linux EtherCAT 主站实现。其核心运行在内核态，用户程序通过字符设备和 `libethercat` 与主站通信。

IgH 主站主要由以下部分组成：

| 组成 | 常见文件或接口 | 作用 |
| --- | --- | --- |
| 主站内核模块 | `ec_master.ko` | 管理 EtherCAT 总线、从站状态、过程数据和时钟同步 |
| 网卡驱动 | `ec_generic.ko` 或专用驱动 | 将主站连接到物理网口；专用驱动通常具有更好的实时性 |
| 字符设备 | `/dev/EtherCAT0` | 内核主站与用户态工具、应用程序之间的接口 |
| 用户态库 | `libethercat.so` | 提供 `ecrt_*` 应用开发 API |
| 命令行工具 | `ethercat` | 扫描从站，查看 PDO、读写 SDO、切换状态和诊断总线 |
| 主站应用 | 用户程序 | 根据设备配置完成周期通信、状态机和业务控制 |

```text
EtherCAT 应用 / ethercat 工具
             │
             ▼
       libethercat.so
             │
             ▼
       /dev/EtherCAT0
             │
             ▼
        ec_master.ko
             │
             ▼
 通用网卡驱动或平台实时网卡驱动
             │
             ▼
        EtherCAT 从站
```

IgH 官方 API 文档：

- [EtherLab EtherCAT Master API](https://docs.etherlab.org/ethercat/1.5/doxygen/index.html)
- [EtherLab EtherCAT 源码](https://gitlab.com/etherlab.org/ethercat)

### 1.3 基本术语

| 术语 | 含义 |
| --- | --- |
| Master | EtherCAT 主站，负责发现、配置和周期访问从站 |
| Slave | EtherCAT 从站，例如伺服驱动器、I/O 模块和传感器 |
| PDO | Process Data Object，周期交换的实时过程数据 |
| SDO | Service Data Object，通常用于非周期参数读写和设备配置 |
| CoE | CAN application protocol over EtherCAT，常见的对象字典协议 |
| DC | Distributed Clocks，EtherCAT 分布式时钟同步机制 |
| ESI | EtherCAT Slave Information，从站厂商提供的 XML 描述文件 |
| Alias | 从站别名地址，可用于稳定标识设备 |
| Position | 从站在总线拓扑中的物理位置，从 `0` 开始 |

EtherCAT 从站通常经历以下状态：

```text
INIT → PREOP → SAFEOP → OP
```

- `INIT`：初始化状态，尚未进行邮箱和过程数据通信。
- `PREOP`：可进行邮箱通信和 SDO 配置，尚未交换有效 PDO。
- `SAFEOP`：输入过程数据有效，输出通常仍处于安全状态。
- `OP`：正常交换输入和输出过程数据。

## 2. 使用前准备

### 2.1 硬件准备

至少需要以下设备：

1. 一台运行 Linux 的主站设备，并提供兼容的独立以太网口。
2. 一个或多个 EtherCAT 从站。
3. 符合设备要求的网线、电源、动力线和编码器线。
4. 从站手册和 ESI XML 文件。
5. 调试伺服系统时所需的急停、限位和机械防护装置。

EtherCAT 从站应按端口方向串联。部分设备明确区分 `IN` 和 `OUT` 端口，接反后可能无法完成扫描。

### 2.2 软件准备

建议准备以下软件环境：

- 与目标板匹配的 Linux 内核源码和交叉编译工具链。
- IgH EtherCAT Master 源码或平台 SDK 提供的预编译包。
- `make`、Autoconf、Automake、Libtool、Pkg-config 和 C 编译器。
- 对实时性要求较高时使用 PREEMPT_RT 内核。
- 使用 DC、TSN 或硬件时间戳时，准备 `linuxptp` 中的 `phc2sys`、`ptp4l` 等工具。

### 2.3 安全说明

EtherCAT 通信正常不代表电机可以安全运行。调试伺服驱动器时应注意：

- 首次测试前断开动力输出或抬高机械负载。
- 先确认急停、限位和驱动器报警逻辑，再使能电机。
- 核对控制字、工作模式、目标位置、目标速度和单位换算。
- 程序退出、链路断开或主站进入错误状态时，应写入安全控制字并停止输出。
- 不要直接将示例中的目标速度、位置或扭矩用于实际设备。

## 3. IgH 主站构建与安装

本章介绍通用 IgH 构建流程。使用 Rockchip SDK 定制包时，优先参考[第 8 章](#8-rockchip-平台支持)，因为其主站和 `stmmac` 驱动包含平台适配。

### 3.1 获取源码

```bash
git clone https://gitlab.com/etherlab.org/ethercat.git
cd ethercat
```

产品开发应固定到经过验证的 tag 或 commit，不建议直接依赖持续变化的 `master` 分支。

### 3.2 配置构建选项

先生成 `configure`：

```bash
./bootstrap
```

本机编译示例：

```bash
./configure \
  --prefix=/opt/etherlab \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-generic=yes
```

交叉编译示例：

```bash
export ARCH=arm64
export CROSS_COMPILE=aarch64-none-linux-gnu-

./configure \
  --host=aarch64-none-linux-gnu \
  --prefix=/opt/etherlab \
  --with-linux-dir=/path/to/kernel \
  --enable-generic=yes
```

常用选项：

| 选项 | 说明 |
| --- | --- |
| `--with-linux-dir=<path>` | 指定已经配置过的目标内核源码目录 |
| `--enable-generic=yes` | 构建通用网卡驱动 `ec_generic` |
| `--enable-<driver>=yes` | 构建 IgH 支持的指定原生网卡驱动 |
| `--enable-wildcards=yes` | 允许主站设备配置使用通配匹配；产品中仍建议固定网口 |
| `--prefix=<path>` | 指定用户态库和工具的安装路径 |

可用驱动选项随 IgH 版本变化，应以当前源码输出为准：

```bash
./configure --help | less
```

### 3.3 编译和安装

```bash
make -j$(nproc) all modules
sudo make modules_install install
sudo depmod -a
```

如果是交叉编译，可先安装到暂存目录：

```bash
make -j$(nproc) all modules
make DESTDIR=$PWD/output modules_install install
```

然后将 `output` 中的内核模块、共享库、头文件、工具和服务文件部署到目标系统的对应目录。

### 3.4 检查安装结果

```bash
find /lib/modules/$(uname -r) -name 'ec_*.ko*'
command -v ethercat
ldconfig -p | grep libethercat
```

如果 `ethercat` 能运行但提示无法打开主站设备，应继续检查内核模块和 `/dev/EtherCAT0`，而不是只检查用户态工具。

## 4. 主站配置与启动

### 4.1 选择 EtherCAT 网口

查看网口和 MAC 地址：

```bash
ip -br link
ip link show eth0
```

选择专用于 EtherCAT 的网口，并记录其永久 MAC 地址。不要使用会随机变化的 MAC 地址。

如果系统服务正在管理该网口，应先取消托管。不同发行版的配置方法不同，例如 NetworkManager 可使用：

```bash
nmcli device set eth0 managed no
```

### 4.2 加载通用 IgH 驱动

通用驱动示例：

```bash
sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_generic
```

将示例 MAC 地址替换为 EtherCAT 专用网口的实际地址。模块参数名称和配置文件路径可能随发行版打包方式变化，可使用以下命令确认：

```bash
modinfo ec_master
modinfo ec_generic
```

某些安装方式提供 `/etc/ethercat.conf` 或 `/etc/sysconfig/ethercat`，可在其中设置主站网口和驱动：

```text
MASTER0_DEVICE="62:36:B8:01:5B:59"
DEVICE_MODULES="generic"
```

配置文件格式应以安装包自带的服务脚本为准。

### 4.3 验证主站

```bash
ls -l /dev/EtherCAT*
sudo ethercat master
sudo ethercat slaves
dmesg | grep -i ethercat
```

正常情况下可以看到主站、链路状态和从站列表。如果没有从站：

1. 检查网线方向、从站电源和链路指示灯。
2. 确认 `main_devices` 使用了正确的 MAC 地址。
3. 使用 `ec_generic` 时确认标准网卡驱动工作正常，但普通网络服务没有在该网口收发 IP 流量。
4. 检查主站加载后绑定的实际设备。
5. 从单个从站开始测试，再逐步恢复完整拓扑。

### 4.4 停止主站

停止应用并确保输出进入安全状态后，再卸载驱动：

```bash
sudo modprobe -r ec_generic
sudo modprobe -r ec_master
```

使用平台专用驱动时，卸载顺序应与加载顺序相反。

## 5. `ethercat` 工具使用

### 5.1 查看帮助

```bash
ethercat --help
ethercat slaves --help
ethercat pdos --help
```

大多数命令支持以下选择方式：

- `-m <master>`：选择主站编号。
- `-p <position>`：按物理位置选择从站。
- `-a <alias>`：按别名地址选择从站。

具体参数以目标系统上的 `ethercat <command> --help` 为准。

### 5.2 查看主站和从站

```bash
ethercat master
ethercat slaves
```

典型从站输出包含位置、别名、状态和设备名称：

```text
0  0:0  PREOP  +  Servo Drive
```

查看详细信息：

```bash
ethercat slaves -v
ethercat config
```

### 5.3 设置别名地址

将位置 `0` 的从站别名设置为 `1`：

```bash
ethercat alias -p 0 1
```

别名会写入从站 EEPROM。执行前应确认目标位置，避免修改错误设备。

### 5.4 查看 PDO

```bash
ethercat pdos -p 0
ethercat cstruct -p 0
```

`ethercat pdos` 显示从站当前 PDO 分配，`ethercat cstruct` 生成可供 C 程序参考的 PDO 条目和同步管理器结构。

自动生成的结构只反映当前设备配置。仍应结合从站手册检查索引、子索引、位宽、方向和同步管理器编号。

### 5.5 查看和读写 SDO

列出对象字典：

```bash
ethercat sdos -p 0
```

读取对象 `0x1018:01`：

```bash
ethercat upload -p 0 0x1018 1
```

指定数据类型读取：

```bash
ethercat upload -p 0 --type uint32 0x1018 1
```

写入 SDO 前必须核对设备手册、数据类型和单位。例如：

```bash
ethercat download -p 0 --type uint16 0x2000 0 1
```

错误的 SDO 写入可能改变电机参数或持久化配置。生产设备上应先备份参数，并避免在电机运行时修改关键对象。

### 5.6 切换状态和重新扫描

```bash
ethercat states PREOP
ethercat states SAFEOP
ethercat states OP
ethercat rescan
```

状态切换失败时，先查看从站错误和主站日志，不要连续强制进入 `OP`。

### 5.7 导出从站信息

```bash
ethercat xml -p 0 > slave.xml
ethercat sii_read -p 0 > slave-sii.bin
```

不同 IgH 版本提供的命令可能不同。写入 SII/EEPROM 风险较高，除非明确掌握从站数据格式，否则只进行读取和备份。

## 6. IgH 应用开发

### 6.1 开发流程

IgH 应用通常按以下顺序初始化：

1. 使用 `ecrt_request_master()` 获取主站。
2. 使用 `ecrt_master_create_domain()` 创建过程数据 Domain。
3. 使用 `ecrt_master_slave_config()` 按别名、位置、Vendor ID 和 Product Code 配置从站。
4. 使用 `ecrt_slave_config_pdos()` 配置同步管理器和 PDO。
5. 使用 `ecrt_domain_reg_pdo_entry_list()` 注册应用需要访问的 PDO 条目。
6. 按需配置 DC、SDO、看门狗和从站专用参数。
7. 使用 `ecrt_master_activate()` 激活主站。
8. 使用 `ecrt_domain_data()` 获取过程数据内存。
9. 进入固定周期的实时循环。
10. 退出前停止输出、释放主站并恢复安全状态。

```text
获取主站 → 创建 Domain → 配置从站和 PDO → 激活主站
                                      │
                                      ▼
接收 → 处理 Domain → 读取输入 → 写入输出 → 排队 → 发送
  ▲                                                     │
  └────────────────── 固定周期循环 ──────────────────────┘
```

### 6.2 生成 PDO 配置参考

先连接并上电从站，然后执行：

```bash
ethercat slaves
ethercat pdos -p 0
ethercat cstruct -p 0
```

将 `cstruct` 输出作为应用配置的起点，并根据从站手册删除无关条目或调整 PDO 映射。Vendor ID 和 Product Code 可通过以下命令确认：

```bash
ethercat slaves -v
ethercat xml -p 0
```

不要只按设备名称匹配从站。应用应校验 Vendor ID、Product Code，并按需要校验 Revision Number。

### 6.3 周期任务基本结构

下面是简化后的调用顺序，用于说明主站 API 的使用关系：

```c
while (running) {
    clock_nanosleep(CLOCK_MONOTONIC, TIMER_ABSTIME,
                    &wakeup_time, NULL);

    ecrt_master_receive(master);
    ecrt_domain_process(domain);

    /* Read inputs from domain_pd with EC_READ_*(). */
    /* Run the device state machine and control algorithm. */
    /* Write outputs to domain_pd with EC_WRITE_*(). */

    ecrt_domain_queue(domain);
    ecrt_master_send(master);
}
```

使用 DC 时，还需要在正确的时序位置设置应用时间并同步时钟：

```c
ecrt_master_application_time(master, app_time_ns);
ecrt_master_sync_reference_clock_to(master, app_time_ns);
ecrt_master_sync_slave_clocks(master);
```

具体同步方式取决于参考时钟、从站能力和控制周期，应以 IgH API 文档及从站手册为准。

### 6.4 编译应用

如果 IgH 安装在 `/opt/etherlab`：

```bash
gcc main.c -o ethercat_app \
  -I/opt/etherlab/include \
  -L/opt/etherlab/lib \
  -Wl,-rpath,/opt/etherlab/lib \
  -lethercat -lpthread
```

交叉编译时使用目标平台编译器，并确保头文件和 `libethercat.so` 来自同一套 IgH 构建结果。

### 6.5 实时线程设置

实时应用通常需要锁定内存并提高周期线程调度优先级：

```c
struct sched_param param = {
    .sched_priority = 80,
};

mlockall(MCL_CURRENT | MCL_FUTURE);
sched_setscheduler(0, SCHED_FIFO, &param);
```

同时建议：

- 使用绝对时间睡眠，避免周期误差累积。
- 在进入实时循环前完成内存分配、文件打开和设备初始化。
- 避免在周期线程中频繁调用 `printf()`、动态分配内存或执行阻塞 I/O。
- 将日志、UI 和网络服务放在非实时线程。
- 周期线程只访问预先分配并正确同步的数据。

提高调度优先级需要相应权限。可以使用 root 运行，或通过系统权限配置授予 `CAP_SYS_NICE` 和锁定内存的能力。

### 6.6 CiA 402 伺服状态机

许多 EtherCAT 伺服驱动器使用 CoE CiA 402 状态机。常见控制流程为：

```text
Fault Reset
    ↓
Shutdown (0x0006)
    ↓
Switch On (0x0007)
    ↓
Enable Operation (0x000F)
    ↓
写入目标位置 / 速度 / 扭矩
```

实际控制字判断必须结合状态字掩码和驱动器手册。不同工作模式对 PDO、单位、使能顺序和停止行为的要求可能不同。

### 6.7 状态监控

应用应周期或按事件检查以下状态：

- `ec_master_state_t`：主站链路、从站数量和 AL 状态。
- `ec_domain_state_t`：Domain 工作计数和工作计数状态。
- `ec_slave_config_state_t`：从站是否在线、是否可操作。
- 从站状态字、错误码和厂商诊断对象。

工作计数不完整通常表示从站掉线、PDO 配置不一致或部分数据报未得到响应。不要在工作计数异常时继续输出危险控制量。

## 7. 实时性优化与调试

### 7.1 PREEMPT_RT

对运动控制等实时性要求较高的业务，建议使用 PREEMPT_RT 内核。仓库内的实时内核配置方法参见 [RTLinux 实时系统开发指南](../RT-Linux/rtlinux.md)。

实时内核只能降低调度延迟，不能替代正确的线程设计、网卡驱动、CPU 隔离和设备配置。

### 7.2 CPU 和调度优化

常见优化措施：

1. 将周期线程绑定到独立 CPU。
2. 将该 CPU 从普通任务和负载均衡中隔离。
3. 将相关网卡 IRQ 绑定到规划好的 CPU。
4. 使用 `SCHED_FIFO`，并为线程选择合理优先级。
5. 将 CPU 调频策略设为 `performance`。
6. 评估是否需要关闭深度 CPU Idle 状态。
7. 避免实时 CPU 上运行图形界面、日志压缩和高负载服务。

查看线程和 IRQ：

```bash
ps -eLo pid,tid,cls,rtprio,psr,comm
cat /proc/interrupts
taskset -pc <pid>
```

设置 CPU 亲和性时应根据 SoC 拓扑、IRQ 分布和业务负载选择 CPU，不能直接复制其他平台的核编号。

### 7.3 性能模式

设置 CPU 调频策略：

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

产品部署时应通过系统服务固定配置，并评估功耗与散热。

### 7.4 周期和抖动测量

如果目标周期为 `T`，可以记录每次实际唤醒时间，计算：

```text
period  = current_start - previous_start
latency = current_start - target_wakeup
jitter  = maximum_period - minimum_period
```

测试时应记录：

- 测试周期和持续时间。
- 最大、最小和分位延迟，而不只记录平均值。
- 从站数量、PDO 大小和 DC 配置。
- CPU 绑定、IRQ 绑定、调频策略和系统负载。
- 是否使用专用网卡驱动、TSN 或硬件时间戳。

可先使用 `cyclictest` 评估系统调度延迟，再运行真实 EtherCAT 应用评估端到端周期。

### 7.5 抓包

可以使用 Wireshark 或 `tcpdump` 检查 EtherCAT 帧：

```bash
sudo tcpdump -i eth0 -e -n ether proto 0x88a4
```

抓包会引入额外负载，只适合诊断，不应长期运行在实时系统中。使用专用驱动时，普通网络抓包接口不一定能观察到全部报文。

### 7.6 常见故障

#### 找不到 `/dev/EtherCAT0`

检查：

```bash
lsmod | grep '^ec_'
dmesg | grep -i ethercat
modinfo ec_master
```

确认主站模块与当前内核版本完全匹配，并检查设备节点规则或主站服务是否正常执行。

#### 主站存在但扫描不到从站

- 检查 MAC 地址和主站绑定网口。
- 检查从站电源、IN/OUT 方向和网线。
- 使用 `ec_generic` 时确认标准网卡驱动正常、普通网络服务未使用该网口；使用原生或平台专用驱动时，确认标准网卡驱动没有占用该设备。
- 检查链路速率；EtherCAT 设备通常使用 100 Mbit/s 全双工。
- 从一个从站和一根短网线开始定位。

#### 从站停留在 PREOP

- 使用 `ethercat slaves -v` 查看 AL 状态和错误码。
- 检查 PDO 分配和同步管理器配置。
- 检查 SDO 初始化是否成功。
- 核对 Vendor ID、Product Code 和 Revision Number。

#### 从站停留在 SAFEOP

- 检查应用是否已开始周期收发。
- 检查工作计数是否完整。
- 检查看门狗、周期、DC 和 Sync0/Sync1 配置。
- 检查输出 PDO 是否按设备要求初始化。

#### 周期抖动较大

- 确认使用 PREEMPT_RT 和适合的实时网卡驱动。
- 检查实时线程是否被其他任务或 IRQ 抢占。
- 检查 CPU 调频、温控降频和深度休眠。
- 移除周期线程中的日志、内存分配和阻塞调用。
- 分别测试空载、单从站和完整拓扑。

#### `ethercat` 无法加载 `libethercat.so`

```bash
ldd "$(command -v ethercat)"
ldconfig -p | grep libethercat
```

将库安装到系统库目录并执行 `ldconfig`，或正确设置运行时库搜索路径。不要混用不同 IgH 版本的工具、库、头文件和内核模块。

## 8. Rockchip 平台支持

### 8.1 支持平台

支持平台如下：

| SoC | 参考内核 | IgH 主站 | Rockchip 实时 `stmmac` | TSN 扩展 |
| --- | --- | --- | --- | --- |
| RK3588 | Linux 5.10 | 支持 | 支持 | 支持 |
| RK3576 | Linux 6.1 | 支持 | 支持 | 支持 |
| RK3568 | Linux 5.10 | 支持 | 支持 | 不支持 |
| RK3506 | Linux 6.1 | 支持 | 支持 | 不支持 |

具体开发板是否引出兼容 GMAC、所用 PHY、设备树配置以及 SDK 是否包含对应补丁，仍需按产品确认。

### 8.2 Rockchip 软件架构

Rockchip 方案整体分为实时内核、主站和驱动、用户态以及应用四层，具体文件如下：

| 部分 | 文件 | 作用 |
| --- | --- | --- |
| 实时内核 | PREEMPT_RT 内核 | 降低调度和中断延迟 |
| IgH 主站 | `ec_master.ko` | EtherCAT 主站核心 |
| 平台网卡驱动 | `ec_stmmac.ko` | Rockchip 针对 GMAC 实时通信适配的 `stmmac` 驱动 |
| 依赖模块 | `phylink.ko`、`pcs-xpcs.ko` | 提供 PHY Link、PCS/XPCS 等网络接口支持 |
| 用户态 | `ethercat`、`libethercat.so` | 调试工具和应用 API |
| 应用 | SDK demo 或产品程序 | 伺服、I/O 和业务控制 |

```text
产品应用 / ethercat 工具
          │
          ▼
    libethercat.so
          │
          ▼
     ec_master.ko
          │
          ▼
     ec_stmmac.ko
          │
          ▼
 Rockchip GMAC / PHY → EtherCAT 从站
```

与通用 `ec_generic` 相比，`ec_stmmac` 直接适配平台 GMAC 数据路径，适合对周期和抖动要求更高的场景。两种驱动不能同时占用同一个物理网口。

### 8.3 SDK 文件位置

源码目录：

```
SDK/external/ethercat_igh
```

### 8.4 配置实时内核

#### 应用 PREEMPT_RT

先根据 SDK 文档应用 PREEMPT_RT 补丁：

```text
SDK/docs/Patches/Real-Time-Performance/README.md
```

也可参考 [RTLinux 实时系统开发指南](../RT-Linux/rtlinux.md)。

#### 将 `stmmac` 编译为模块

IgH 平台驱动需要接管 GMAC，因此不能继续将标准 `stmmac` 固定编译进内核。5.10 和 6.1 内核通常需要：

```text
CONFIG_STMMAC_ETH=m
```

检查最终配置：

```bash
grep CONFIG_STMMAC_ETH .config
```

如果标准 `stmmac` 已经内建并占用网口，后加载的 `ec_stmmac.ko` 无法接管设备。

#### CPU 隔离和 Tick 配置

参考方案为实时任务预留一个 CPU，并启用完整 Tick 隔离：

```text
CONFIG_NO_HZ_FULL=y
```

启动参数示例：

```text
isolcpus=<cpu> nohz_full=<cpu>
```

建议在 RK3568 上使用 CPU3，在 RK3576 上使用 CPU7。实际产品必须根据 CPU 拓扑、IRQ 和业务负载重新选择，不能直接复制核编号。

必要时还可关闭隔离 CPU 的深度休眠，或通过内核配置关闭相关 CPU Idle 功能。该操作会增加功耗和温度，应通过长时间测试评估。

### 8.5 编译 

直接到目标机器编译

```bash
./bootstrap

# 可通过--prefix=指定用户态程序的安装目录
./configure \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-8139too=no \
  --enable-stmmac=yes \
  --enable-generic=no \
  --enable-wildcards=yes

```

编译并暂存安装文件：

```bash
make -j8 all modules
make modules_install install
```

使用 `modprobe` 和模块依赖管理：

```bash
sudo depmod -a

sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_stmmac
```

将 `main_devices` 替换为 EtherCAT GMAC 的真实 MAC 地址：

```bash
ip link
cat /sys/class/net/eth0/address
```

加载后检查：

```bash
dmesg | tail -n 100
ls -l /dev/EtherCAT0
ethercat master
ethercat slaves
```

如果日志中平台 `stmmac` 驱动的 `ecdev` 为 `0x0`，最常见原因是 `main_devices` 的 MAC 地址错误。还应检查目标 GMAC 是否已被标准驱动占用，以及设备树是否选择了正确网口。

### 8.6 启动实时应用

将 CPU 设为性能模式：

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

确认总线后再启动应用：

```bash
ethercat slaves
ethercat pdos
ethercat cstruct
sudo ./ethercat_app
```

### 8.7 双网口配置

双 GMAC 产品可以将一个网口专用于 EtherCAT，另一个保留为标准以太网。例如：

```text
gmac0 → EtherCAT IgH
gmac1 → TCP/IP、SSH 和普通网络业务
```

参考 RK3568 设备树通过专用 compatible 标记 EtherCAT GMAC：

```dts
gmac0: ethernet@fe2a0000 {
    compatible = "rockchip,rk3568-gmac-ethercat", "snps,dwmac-4.20a";
    reg = <0x0 0xfe2a0000 0x0 0x10000>;
};
```

使用双网口时注意：

- `main_devices` 必须填写 EtherCAT 网口的 MAC，而不是普通网口的 MAC。
- EtherCAT 网口不配置 IP，也不交给 NetworkManager 管理。
- 普通网口继续使用标准 `stmmac` 驱动。
- 两个 GMAC 的设备树、时钟、复位、PHY 和 pinctrl 必须分别正确配置。
- 专用 compatible 和驱动支持依赖对应 SDK 补丁，不能直接用于未适配的内核。

### 8.8 性能参考

参考文档在 `1 ms` 控制周期下给出的平台数据如下：

| SoC | 控制周期 | 普通模式抖动 | TSN 模式抖动 |
| --- | --- | --- | --- |
| RK3588 | 1 ms | 15 µs | 1 µs |
| RK3576 | 1 ms | 30 µs | 1 µs |
| RK3568 | 1 ms | 50 µs | N/A |
| RK3506 | 1 ms | 100 µs | N/A |

这些数据仅用于理解参考方案的量级，不是所有开发板和系统的保证值。实际结果会受到以下因素影响：

- SDK、内核、PREEMPT_RT 和驱动版本。
- CPU 隔离、IRQ 亲和性、调频和温控策略。
- PHY、网线、从站数量、PDO 大小和拓扑。
- 应用线程设计、日志、UI 和系统后台负载。
- 抖动统计方法以及是否测量真实硬件发送时刻。

产品验收应在目标硬件、最终镜像和最大业务负载下长时间测试，并记录最坏值和高分位值。

### 8.9 量产建议

- 固定 IgH、内核、实时补丁和平台驱动的 commit。
- 固化 EtherCAT 网口、MAC 地址、模块加载顺序和 CPU/IRQ 亲和性。
- 启动服务应等待模块、设备节点和从站就绪，并提供失败回退。
- 应用退出、看门狗超时和链路中断时强制进入安全输出。
- 保存从站 ESI、PDO/SDO 配置、固件版本和参数备份。
- 建立空载、满载、长稳、高低温和断线恢复测试。
