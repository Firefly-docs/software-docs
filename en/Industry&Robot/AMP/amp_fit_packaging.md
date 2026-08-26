# AMP FIT Packaging

## 6. FIT packaging configuration

AMP firmware is packaged in the U-Boot FIT format. The following is a simplified Linux + CPU3 RT-Thread example:

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

> Important: these addresses only explain the RK3562 example. They are not universal addresses for all Firefly boards. Always start from the validated ITS and DTS files in the target SDK.

### 6.1 Important properties

| Property | Description |
| --- | --- |
| `data` | RTOS/HAL binary to package |
| `type` | AP firmware is normally `firmware`; MCU firmware is normally `standalone` |
| `arch` | Remote firmware instruction set, such as `arm` or `arm64` |
| `cpu` | Hardware CPU ID for an AP core |
| `load` | Physical load and execution address of the firmware |
| `size` | Private runtime memory size assigned to the remote core |
| `sys` | `rtt` or `hal` |
| `core` | `ap` or `mcu` |
| `udelay` | Delay before the next core is started, in microseconds |
| `loadables` | Images to load and their ordering |
| `shm_*` | Base address and size of general shared memory |
| `rpmsg_*` | Base address and size of RPMsg shared memory |

When using multiple remote cores, define a separate child node under `images` for each core. Their private memory regions must not overlap, and every name in `loadables` must match an image node.
