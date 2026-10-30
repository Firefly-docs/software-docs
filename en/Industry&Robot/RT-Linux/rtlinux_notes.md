# More Real-Time Optimization Options

## Cache Partitioning

Cache partitioning divides the last-level cache (LLC) into non-overlapping regions and limits different tasks or task groups to their own cache regions. This reduces cache contention and memory access latency jitter. On multicore platforms, when real-time tasks share the LLC with memory-intensive general-purpose tasks, the latter can continuously evict the working set of the former, causing unpredictable cache misses. Cache partitioning cuts off this interference through spatial isolation.

### Example

The RK3568 uses the ARM Cortex-A55 architecture, which supports L3 space partitioning. Cortex-A55 L3 memory can be divided into four parts, and each CPU can be configured to use selected parts. Configure this in `rkbin/RKBOOT/RK3568MINIALL.ini`:

```c
[BOOT1_PARAM]
WORD_3=0xcc33
```

`WORD_3=0xcc33` means that the four L3 regions are labeled P0, P1, P2, and P3 as follows:

- CPU0 and CPU1 share L3 P0 and P1.
- CPU2 and CPU3 share L3 P2 and P3.

After configuration, confirm it through the following boot log:

```shell
INFO: L3 cache partition cfg-cc33
```

## CPU Isolation

CPU isolation removes designated CPUs from the general scheduler and housekeeping subsystem through kernel boot parameters. Isolated CPUs do not participate in load balancing or run routine kernel background work. They are usually dedicated to real-time tasks to reduce interference from scheduler migrations, kernel worker threads, and periodic ticks.

### Example

Add `isolcpus=3 nohz_full=3` to `bootargs` to isolate CPU3 so that it does not participate in system task scheduling and can be used as the real-time core.

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

## Run Real-Time Tasks on the Real-Time Core

Task-to-core affinity uses CPU affinity to pin a specific task or thread to one or more designated CPUs. It is a basic mechanism for CPU partitioning. It is commonly combined with a real-time scheduling policy such as `SCHED_FIFO`, allowing real-time tasks to run at high priority on a dedicated core while reducing migration overhead and loss of cache locality.

### Example

Bind `cyclictest` to CPU3 to test real-time performance:

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```
