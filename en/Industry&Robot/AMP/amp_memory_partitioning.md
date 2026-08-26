# AMP Memory Partitioning

## 7. Memory resource partitioning

Memory conflicts are among the most common AMP problems and can cause intermittent crashes. A remote core's private memory region must be represented consistently in at least three places:

1. The ITS `load` and `compile/size` properties.
2. The RT-Thread/HAL linker script or build script firmware base and size.
3. Linux DTS `reserved-memory`.

### 7.1 Reserve remote-core memory in Linux

The following example reserves the remote-core runtime region `0x01800000` through `0x01ffffff`:

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

Linux does not add `no-map` memory to the normal page allocator, preventing it from overwriting remote-core code and data.

### 7.2 Reserve RPMsg shared memory

Rockchip examples commonly assign 5 MiB to RPMsg: 4 MiB for vrings and 1 MiB for the vdev buffer/DMA pool. For example:

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

The current Rockchip Linux RPMsg solution uses uncached shared memory by default. If it is changed to cacheable memory, both sides must implement correct cache clean/invalidate operations and memory barriers. Otherwise, lost messages, stale data, or vring corruption can occur.

### 7.3 AP and MCU address differences

Addresses in AP linker scripts are normally physical DDR addresses. An MCU often sees its load address as local address `0x0`, so MCU-visible addresses have a fixed offset from physical addresses.

For example, if the MCU is loaded at `0x08200000` and its linker script places shared memory at offset `0x00100000`, the physical address is:

```text
0x08200000 + 0x00100000 = 0x08300000
```

When changing the MCU load address or RPMsg shared memory, check all of the following:

- The `load` property in `amp_mcu.its`.
- The MCU linker script.
- MCU memory mapping and the uncached region in U-Boot.
- Physical/virtual address conversion in `rpmsg_platform.c`.
- Linux DTS `reserved-memory`.
