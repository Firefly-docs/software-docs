# EtherCAT Rockchip 平台支持

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
