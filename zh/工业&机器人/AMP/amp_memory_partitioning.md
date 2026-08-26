# AMP 内存资源划分

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
