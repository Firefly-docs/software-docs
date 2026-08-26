# EtherCAT Rockchip 实时内核

## 8. Rockchip 平台支持

### 8.4 配置实时内核

#### 应用 PREEMPT_RT

先根据 SDK 文档应用 PREEMPT_RT 补丁：

```text
SDK/docs/Patches/Real-Time-Performance/README.md
```

也可参考 [RTLinux 实时系统开发指南](../RT-Linux/rtlinux_preempt_rt.md)。

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
