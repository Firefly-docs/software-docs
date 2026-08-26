# EtherCAT 概述

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
