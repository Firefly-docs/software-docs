# RTLinux Notes

## Notes

### RK3568 Requires the RT Version of BL31

Using the RT version of BL31 on RK3568 provides better real-time performance.

Build command:

```shell
$ cd $sdk/uboot
$ ./make.sh rk3568-rt
```

Flash `miniloader.bin` and `uboot.img`.

If `cache_write_streaming_cfg` related messages appear during boot, the RT version of BL31 is being used.

### Methods to Improve Real-Time Performance on RK3568

#### Cache Partitioning

The ARM Cortex-A55 architecture supports L3 space partitioning. Cortex-A55 L3 memory can be divided into 4 parts, and each CPU can be configured to use specific parts. Configure this in `rkbin/RKBOOT/RK3568MINIALL.ini`:

```c
[BOOT1_PARAM]

WORD_3=0xcc33
```

`WORD_3=0xcc33` means the 4 L3 parts are labeled P0, P1, P2, and P3 as follows:

- CPU0 and CPU1 share L3 P0 and P1.
- CPU2 and CPU3 share L3 P2 and P3.

The `WORD_3` configuration value is described below:

- bit0~bit3: the mask bits of the 4 L3 parts allocated to CPU0. bit0 set to 1 means the first L3 part is allocated to CPU0; bit1 set to 1 means the second L3 part is allocated to CPU0, and so on.
- bit4~bit7: the mask bits of the 4 L3 parts allocated to CPU1.
- bit8~bit11: the mask bits of the 4 L3 parts allocated to CPU2.
- bit12~bit15: the mask bits of the 4 L3 parts allocated to CPU3.

After configuration, confirm it through the following boot log:

```shell
INFO: L3 cache partition cfg-cc33
```

#### Isolate a Core

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

#### Bind Real-Time Tasks to the Real-Time Core

Bind `cyclictest` to CPU3 to test real-time performance:

```shell
$ taskset -c 3 cyclictest -c0 -m -t -p99 -D 12h
```

> Note: Use `ps -eo pid,psr,comm | grep cyclictest` to check whether `cyclictest` is bound to CPU3.
