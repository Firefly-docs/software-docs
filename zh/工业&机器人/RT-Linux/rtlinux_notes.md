# 更多实时优化方案

## cache 分片
Cache 分片（Cache Partitioning）是将最后一级缓存（LLC）划分为互不重叠区域、使不同任务或任务组限定在各自缓存区域内活动的技术，用于减少共享缓存上的相互冲刷与访存延迟抖动。在多核平台上，实时任务与内存密集型普通任务共享 LLC 时，后者可能持续驱逐前者工作集，导致实时任务出现不可预测的 cache miss。Cache 分片通过空间隔离切断该干扰路径。

### 使用示例
RK3568使用 ARM Cortex-A55 架构，该架构支持对 L3 空间进行划分。Cortex-A55 L3 内存空间可划分为 4 块，可以配置每个 CPU 使用其中的哪几块。该配置在 `rkbin/RKBOOT/RK3568MINIALL.ini` 文件中设置：

```c
[BOOT1_PARAM]
WORD_3=0xcc33
```

`WORD_3=0xcc33` 表示以 P0、P1、P2、P3 标记 L3 的 4 块空间时：

- CPU0、CPU1 共享 L3 的 P0、P1。
- CPU2、CPU3 共享 L3 的 P2、P3。

配置后可通过以下开机日志确认：

```shell
INFO: L3 cache partition cfg-cc33
```

## 隔离核心
隔离核心（CPU Isolation）是通过内核启动参数将指定 CPU 从通用调度与 housekeeping 子系统中剥离，使其不参与负载均衡、不承载常规内核后台工作的技术。被隔离核心通常专供实时任务使用，以减少调度器迁移决策、内核工作线程与周期性 tick 带来的干扰。

### 使用示例
在 `bootargs` 中添加 `isolcpus=3 nohz_full=3`，将 CPU3 隔离出来，使其不参与系统任务调度，并作为实时核心使用。

```diff
diff --git a/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi b/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
index c7e309645099b..28fac4880744d 100644
--- a/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
+++ b/arch/arm64/boot/dts/rockchip/rk3568-linux.dtsi
@@ -13,7 +13,7 @@ aliases {
        };
        chosen: chosen {
-               bootargs = "earlycon=uart8250,mmio32,0xfe660000 console=ttyFIQ0 root=PARTUUID=614e0000-0000 rw rootwait";
+               bootargs = "earlycon=uart8250,mmio32,0xfe660000 isolcpus=3 nohz_full=3 console=ttyFIQ0 root=PARTUUID=614e0000-0000 rw rootwait";
        };
        fiq-debugger {
```

## 实时任务绑定到实时核上运行
任务绑定核心（Task-to-Core Affinity）是通过 CPU 亲和性将特定任务或线程固定在一个或一组指定 CPU 上运行的技术，属于 CPU 分区的基础机制。通常与实时调度策略（如 SCHED_FIFO）配合使用，使实时任务在专属核上以高优先级运行，减少迁移开销与缓存局部性丢失。

### 使用示例
将 `cyclictest` 绑定到 CPU3 上运行，测试实时性能：

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```
