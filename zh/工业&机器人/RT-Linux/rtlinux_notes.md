# RTLinux 注意事项

## 注意事项

### RK3568 需要使用 RT 版本的 BL31

RK3568 使用 RT 版本的 BL31 可获得更好的实时性能。

编译命令：

```shell
$ cd $sdk/uboot
$ ./make.sh rk3568-rt
```

烧录 `miniloader.bin` 和 `uboot.img`。

开机过程中出现 `cache_write_streaming_cfg` 相关打印，说明已经使用 RT 版本的 BL31：

### RK3568 提高实时性的做法

#### cache 分片

ARM Cortex-A55 架构支持对 L3 空间进行划分。Cortex-A55 L3 内存空间可划分为 4 块，可以配置每个 CPU 使用其中的哪几块。该配置在 `rkbin/RKBOOT/RK3568MINIALL.ini` 文件中设置：

```c
[BOOT1_PARAM]

WORD_3=0xcc33
```

`WORD_3=0xcc33` 表示以 P0、P1、P2、P3 标记 L3 的 4 块空间时：

- CPU0、CPU1 共享 L3 的 P0、P1。
- CPU2、CPU3 共享 L3 的 P2、P3。

`WORD_3` 配置值说明如下：

- bit0~bit3：分配给 CPU0 的 4 份 L3 mask bit。bit0 为 1 表示 L3 的第一份分给 CPU0，bit1 为 1 表示 L3 的第二份分给 CPU0，以此类推。
- bit4~bit7：分配给 CPU1 的 4 份 L3 mask bit。
- bit8~bit11：分配给 CPU2 的 4 份 L3 mask bit。
- bit12~bit15：分配给 CPU3 的 4 份 L3 mask bit。

配置后可通过以下开机日志确认：

```shell
INFO: L3 cache partition cfg-cc33
```

#### 隔离核心

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

#### 实时任务绑定到实时核上运行

将 `cyclictest` 绑定到 CPU3 上运行，测试实时性能：

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```

> 注：`ps -eo pid,psr,comm | grep cyclictest` 可以查看 `cyclictest` 是否绑定在 CPU3。
