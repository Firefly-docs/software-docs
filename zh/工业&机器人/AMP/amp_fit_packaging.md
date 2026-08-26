# AMP FIT 打包配置

## 6. FIT 打包配置

AMP 固件使用 U-Boot FIT 格式打包。下面是一个简化的 Linux + CPU3 RT-Thread 示例：

```dts
/dts-v1/;

/ {
    description = "Rockchip AMP FIT Image";
    #address-cells = <1>;

    images {
        amp3 {
            description = "rtt-core3";
            data = /incbin/("rtt3.bin");
            type = "firmware";
            compression = "none";
            arch = "arm";
            cpu = <3>;
            load = <0x01800000>;

            compile {
                size = <0x00800000>;
                sys = "rtt";
                core = "ap";
                rtt_config = "board/<board>/amp_defconfig";
            };

            udelay = <10000>;
            hash {
                algo = "sha256";
            };
        };
    };

    share {
        shm_base = <0x07800000>;
        shm_size = <0x00400000>;
        rpmsg_base = <0x07c00000>;
        rpmsg_size = <0x00500000>;
    };

    configurations {
        default = "conf";
        conf {
            loadables = "amp3";
            linux {
                arch = "arm64";
                cpu = <0>;
                load = <0x02000000>;
                load_c = <0x04880000>;
            };
        };
    };
};
```

> 重要：上述地址仅用于解释 RK3562 示例，不是 Firefly 所有板卡的通用地址。必须以目标 SDK 内已验证的 ITS 和 DTS 为起点。

### 6.1 关键属性

| 属性 | 说明 |
| --- | --- |
| `data` | 需要打包的 RTOS/HAL 二进制文件 |
| `type` | AP 固件通常为 `firmware`，MCU 固件通常为 `standalone` |
| `arch` | 从核固件的指令集，例如 `arm` 或 `arm64` |
| `cpu` | AP 的硬件 CPU ID |
| `load` | 固件的物理加载和运行地址 |
| `size` | 该从核的私有运行内存大小 |
| `sys` | `rtt` 或 `hal` |
| `core` | `ap` 或 `mcu` |
| `udelay` | 启动下一个核前的延时，单位为微秒 |
| `loadables` | 需要加载的镜像和顺序 |
| `shm_*` | 普通共享内存起始地址和大小 |
| `rpmsg_*` | RPMsg 共享内存起始地址和大小 |

多个从核时，每个核使用独立的 `images` 子节点。各节点的私有内存不得重叠，`loadables` 中的名称必须与节点名一致。
