# AMP User Guide

## 1. Overview

AMP (Asymmetric Multi-Processing) allows Linux, RT-Thread, or bare-metal programs to run independently on different processor cores of the same SoC. These systems share one SoC, but use separately allocated CPU cores, memory, interrupts, and peripherals.

AMP is commonly used when a product requires both the Linux software ecosystem and hard real-time behavior, for example:

- Industrial control, robot motion control, and data acquisition.
- Power protection and real-time signal processing.
- Linux handling networking, graphics, storage, and algorithms while an RTOS or MCU handles fast responses.
- Integrating a traditional "main processor + external MCU" design into a single SoC.

The Rockchip AMP SDK mainly provides:

- Loading and starting remote-core firmware from U-Boot.
- Linux, RT-Thread, and RK HAL bare-metal development environments.
- CPU, memory, interrupt, pin, clock, and peripheral resource partitioning.
- RPMsg/RPMsg-Lite communication based on inter-core interrupts and shared memory.

The Rockchip solution uses an unsupervised AMP architecture by default and does not depend on a hypervisor. This reduces the interrupt latency introduced by virtualization, but developers must ensure that resources assigned to different systems do not conflict.

### 1.1 System roles

```text
Linux / first RTOS to start (Master Core)
              │
              ├── Allocates CPUs, memory, and shared peripherals
              ├── Loads, starts, and manages remote cores
              └── Manages RPMsg shared memory
                         │
                         └── RT-Thread / bare metal (Remote Core)
```

- Linux + RTOS/bare metal: the Linux core is the master, and the RTOS/bare-metal core is a remote core.
- RTOS + bare metal: the first processor core to start is the master.
- AP + MCU: the Linux AP is the master and the MCU is the remote core.

## 2. Platform support

| SoC | Processor cores | Linux | RTOS | Bare metal |
| --- | --- | --- | --- | --- |
| RK3588 | 4 × Cortex-A76 | Kernel 5.10 | Not supported | Not supported |
| RK3588 | 4 × Cortex-A55 | Kernel 5.10 | RT-Thread 3.1/4.1, 32-bit | HAL, 32-bit |
| RK3588 | 1 × Cortex-M0 | Not supported | RT-Thread 3.1/4.1 | HAL |
| RK3576 | 4 × Cortex-A72 | Kernel 6.1 | Not supported | Not supported |
| RK3576 | 4 × Cortex-A53 | Kernel 6.1 | RT-Thread 4.1, 32-bit | HAL, 32-bit |
| RK3576 | 1 × Cortex-M0 | Not supported | RT-Thread 4.1 | HAL |
| RK3568 | 4 × Cortex-A55 | Kernel 4.19/5.10 | RT-Thread 3.1, 32-bit | HAL, 32-bit |
| RK3568 | 1 × RISC-V | Not supported | RT-Thread 3.1 | HAL |
| RK3562 | 4 × Cortex-A53 | Kernel 5.10 | RT-Thread 4.1, 32-bit | HAL, 32-bit |
| RK3562 | 1 × Cortex-M0 | Not supported | RT-Thread 4.1 | HAL |
| RK3358 | 4 × Cortex-A35 | Not supported | RT-Thread 3.1, 32-bit | HAL, 32-bit |
| RK3308 | 4 × Cortex-A35 | Kernel 5.10 | RT-Thread 3.1/4.1, 32-bit | HAL, 32-bit |

> Note: "32-bit" in this table means that the RTOS/HAL firmware on an AP runs in AArch32 state. Linux can continue to run in AArch64 state.

## 3. AMP SDK structure

The directories directly related to heterogeneous development are shown below:

```text
<SDK>/
├── device/rockchip/       # Unified build scripts, board defconfigs, and FIT ITS files
├── kernel/ or kernel-*    # Linux Kernel, AMP management driver, and RPMsg
├── hal/                   # RK HAL bare-metal library and examples
├── rtos/                  # RT-Thread, board configuration, and RPMsg-Lite
├── u-boot/                # AMP FIT loading and remote-core startup
├── rkbin/                 # BL31, Loader, and special boot configurations
├── prebuilts/             # Cross-compilation toolchains
└── tools/                 # Packaging and firmware tools
```

Common files and their purposes:

| File | Purpose |
| --- | --- |
| `device/rockchip/.chip/rockchip_xxx_defconfig` | Selects AMP projects, architecture, ITS, U-Boot configuration, and partition table |
| `device/rockchip/<soc>/amp_linux.its` | Packaging description for Linux + AP RTOS/bare metal |
| `device/rockchip/<soc>/amp.its` | Packaging description for RTOS + bare metal |
| `device/rockchip/<soc>/amp_mcu.its` | Packaging description for Linux + MCU RTOS/bare metal |
| `kernel/arch/arm64/boot/dts/rockchip/*-amp.dts*` | Linux-side reserved memory, interrupts, pins, clocks, and peripheral assignment |
| `kernel/drivers/soc/rockchip/rockchip_amp.c` | AMP resources and remote-core lifecycle management |
| `rtos/bsp/rockchip/<target>/` | RT-Thread board project |
| `hal/project/<target>/` | Bare-metal board project |

Directory names may differ slightly between SDK releases. Use the following commands to locate the actual files:

```bash
find device/rockchip -name 'amp*.its' -o -name '*amp*defconfig'
find kernel* -path '*rockchip*' -name '*amp*.dts*'
```

## 4. Prerequisites

### 4.1 Hardware and software

- A Firefly board that supports AMP.
- A Firefly/Rockchip AMP SDK matching the board and SoC.
- The build dependencies and cross-compilation toolchain required by the SDK.
- A serial adapter. It is recommended to connect both the Linux debug UART and the RTOS/HAL debug UART.
- A recoverable full firmware image in case an invalid memory or boot configuration prevents normal startup.

The default AMP debug UART parameters are usually:

| Baud rate | Data bits | Stop bits | Parity | Flow control |
| --- | --- | --- | --- | --- |
| 1500000 | 8 | 1 | none | none |

> The actual debug UART and IOMUX configuration depend on the target board. Do not directly reuse UART pins from another board.

### 4.2 Plan resources first

Create a resource allocation table before building the firmware:

| Resource | Linux | Remote Core | Checks |
| --- | --- | --- | --- |
| CPU | CPU0 through CPUn | Specified AP core or MCU | An AP core cannot belong to both Linux SMP and an RTOS |
| Private memory | Linux DRAM | RTOS/HAL firmware region | DTS `reserved-memory`, ITS `load/size`, and the linker script must agree |
| Shared memory | RPMsg/shared memory | Same physical region | Must not overlap CMA, display buffers, NPU memory, or another reserved region |
| Peripheral | Linux driver | RTOS/HAL driver | Only one system may actively manage a device |
| Interrupt | Linux GIC | GIC/NVIC/IPIC | Core mapping, interrupt number, and routing must agree |
| Pin | Linux pinctrl | HAL IOMUX | Check the pinmux group and electrical settings |
| Clock/power domain | Managed by the Linux AMP driver | Used by RTOS/HAL | Linux must not disable the clock or power domain because it sees no consumer |

## 5. Quick start

The following procedure uses the unified SDK build script. Menu entries and target names can differ between releases; use `./build.sh help` as the authoritative reference for the current SDK.

### 5.1 Select the chip and board configuration

Run the following commands from the SDK root directory:

```bash
./build.sh chip
./build.sh lunch
```

Select an AMP-enabled defconfig that exactly matches the Firefly board. Then inspect the selected configuration:

```bash
grep -E '^RK_AMP|^RK_UBOOT_CFG_FRAGMENTS|^RK_PARAMETER' \
  device/rockchip/.chip/rockchip_*_defconfig
```

The important options are:

```text
RK_AMP=y                         # Enable AMP builds
RK_AMP_ARCH="arm"               # Use AArch32 for AP RTOS/HAL; use arm64 for 64-bit
RK_AMP_HAL_TARGET="<target>"     # AP bare-metal project
RK_AMP_RTT_TARGET="<target>"     # AP RT-Thread project
RK_AMP_MCU_HAL_TARGET="<target>" # MCU bare-metal project
RK_AMP_MCU_RTT_TARGET="<target>" # MCU RT-Thread project
RK_AMP_FIT_ITS="amp_linux.its"   # AMP FIT packaging configuration
RK_UBOOT_CFG_FRAGMENTS="rk-amp"  # Enable AMP in U-Boot
RK_PARAMETER="parameter.txt"     # Partition table
```

If the SDK provides a `menuconfig` interface, prefer it over manually editing configuration files so that dependencies are preserved.

### 5.2 Select an AMP combination

| Target combination | ITS file | Description |
| --- | --- | --- |
| Linux + AP RT-Thread/HAL | `amp_linux.its` | Linux runs on the master core and one or more AP cores run an RTOS/HAL |
| RT-Thread + bare metal | `amp.its` | AP cores run RTOS/HAL as required, without Linux |
| Linux + MCU RT-Thread/HAL | `amp_mcu.its` | The AP runs Linux and the SoC's internal MCU runs an RTOS/HAL |

Verify the `cpu`, `load`, `size`, `sys`, and `loadables` properties for every remote-core image in the ITS file. Never change only `load` without updating the Linux DTS and RTOS/HAL linker configuration.

### 5.3 Build

Build and package the complete SDK:

```bash
./build.sh
```

Rebuild only the AMP firmware:

```bash
./build.sh amp
```

Build U-Boot and the Linux Kernel separately:

```bash
./build.sh uboot
./build.sh kernel
```

Clean and rebuild everything:

```bash
./build.sh cleanall
./build.sh
```

`./build.sh amp` parses the `compile` nodes in the ITS file, builds the corresponding RT-Thread/HAL projects, and generates `amp.img`. Locate the output with:

```bash
find . -name amp.img -printf '%TY-%Tm-%Td %TH:%TM  %p\n' | sort
```

### 5.4 Build individual components

The following RK3562 example builds the Linux Kernel separately:

```bash
cd <SDK>/kernel
export ARCH=arm64
export CROSS_COMPILE=<SDK>/prebuilts/gcc/linux-x86/aarch64/<toolchain>/bin/aarch64-none-linux-gnu-
make rockchip_linux_defconfig
make <board>-linux-amp.img -j$(nproc)
```

AP RT-Thread:

```bash
cd <SDK>/rtos/bsp/rockchip/<ap-target>/
./build.sh <cpu_id|all>
./mkimage.sh
```

AP bare metal:

```bash
cd <SDK>/hal/project/<ap-target>/GCC
./build.sh <cpu_id|all>
cd ..
./mkimage.sh
```

U-Boot:

```bash
cd <SDK>/u-boot
make <soc>_defconfig rk-amp.config
./make.sh
```

> Replace `<board>`, `<ap-target>`, `<soc>`, and the toolchain path with the names used by the current Firefly SDK.

### 5.5 Configure the AMP partition

When booting from eMMC or SPI flash, the partition table needs an `amp` partition large enough for `amp.img`. First display the current partition table:

```bash
./build.sh list-parts
```

For example, insert a 2 MiB AMP partition at position 4:

```bash
./build.sh insert-part:4:amp:2M
./build.sh list-parts
```

The partition must be larger than the actual `amp.img` and should include room for future growth. After changing the partition table, update `parameter.txt` as required or regenerate the complete firmware image.

### 5.6 Flash and verify startup

Use the firmware upgrade procedure for the target Firefly board:

- If the partition table changed, flash the newly generated complete firmware image.
- If the partition table did not change, update `uboot.img`, `boot.img`, and `amp.img` individually according to the board's upgrade guide.
- If the solution uses a special BL31 or Loader, update the corresponding boot firmware as well.

During boot, first confirm that U-Boot successfully released the remote core. A typical RK3562 CPU3 message is:

```text
AMP: Brought up cpu[3] with state 0x10, entry 0x01800000 ...OK
```

Typical bare-metal remote-core output:

```text
Hello RK3562 Bare-metal using RK_HAL!
CPI_ID(3)
CPU(3) Initial OK!
```

An RT-Thread remote core should print its RT-Thread version and then enter the shell or main application.

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

## 8. Peripheral resource partitioning

The Linux DTS describes most SoC peripherals by default. Assigning a peripheral to RTOS/HAL requires transferring the entire ownership of that peripheral, not merely disabling its Linux driver.

For example, use the following sequence to assign I2C1 to a remote core:

1. Disable I2C1 in the Linux board DTS.
2. Add the I2C1 clocks, pinctrl, and interrupt to the `rockchip-amp` node.
3. Configure the same IOMUX, clocks, and interrupt routing in RT-Thread/HAL.
4. Confirm that no other Linux node references the peripheral or its pins.

Disable the device on the Linux side:

```dts
&i2c1 {
    status = "disabled";
};
```

Example AMP node structure:

```dts
rockchip_amp: rockchip-amp {
    compatible = "rockchip,amp";

    /* Append I2C1 clocks to the node's existing clocks list. */
    clocks = <&cru CLK_I2C1>, <&cru PCLK_I2C1>;

    /* Append the I2C1 pinctrl group to the node's existing pin list. */
    pinctrl-names = "default";
    pinctrl-0 = <&i2c1m0_xfer>;

    /* Append the I2C1 interrupt to the node's existing amp-irqs list. */
    amp-irqs = /bits/ 64 <
        GIC_AMP_IRQ_CFG_ROUTE(45, 0xd0, CPU_GET_AFFINITY(3, 0))
    >;

    status = "okay";
};
```

> In the RK3562 I2C1 example, the peripheral interrupt number in the DTS is 13, while the SPI interrupt passed to the GIC AMP configuration is `13 + 32 = 45`. Interrupt numbering rules and macros can differ by SoC and GIC version. Use an existing AMP DTS from the target SDK as the reference.

### 8.1 RT-Thread side

Common RT-Thread files include:

```text
rtos/bsp/rockchip/<target>/board/common/board_base.c
rtos/bsp/rockchip/<target>/board/common/iomux_base.c
rtos/bsp/rockchip/<target>/board/<board>/board.c
rtos/bsp/rockchip/<target>/board/<board>/iomux.c
```

Functions and structures declared with `RT_WEAK` in the common implementation can be redefined by board-specific files. Keep IOMUX and board-specific peripheral setup in the board directory instead of changing the common initialization sequence.

An AP peripheral interrupt must be routed to the current CPU in the GIC configuration table:

```c
#define CUR_CPU 3

static struct GIC_AMP_IRQ_INIT_CFG irqsConfig[] = {
    GIC_AMP_IRQ_CFG_ROUTE(I2C1_IRQn, 0xd0,
                          CPU_GET_AFFINITY(CUR_CPU, 0)),
};
```

Interrupts directly connected to an MCU use NVIC. Other MCU peripheral interrupts are normally forwarded through INTMUX. The RK3568 RISC-V core uses IPIC/INTMUX.

### 8.2 Bare-metal side

RK HAL projects normally perform the following operations in `main.c` or board-specific files:

1. Call `HAL_Init()` and `BSP_Init()`.
2. On an AP, call `HAL_GIC_Init()` to initialize GIC routing.
3. Call `HAL_PINCTRL_SetIOMUX()` to configure pins.
4. Obtain the clock rate and initialize the peripheral.
5. Register the interrupt handler and enable the interrupt.

## 9. RPMsg inter-core communication

The underlying Rockchip AMP communication model is:

```text
Sender writes shared memory
          │
          ├── Updates vring queue state
          └── Triggers an inter-core interrupt through Mailbox / SoftIRQ / SGI
                                      │
                                      └── Receiver obtains and processes the message
```

Linux uses the in-kernel RPMsg/VirtIO framework, while RT-Thread and bare-metal systems use RPMsg-Lite. RPMsg consists of two unidirectional vrings and a vdev buffer. The Master Core manages the shared memory.

The default RPMsg buffer size in the SDK is 512 bytes. After subtracting the 16-byte RPMsg header, the maximum payload of one message is 496 bytes. Large data should be fragmented at the application layer, or transferred through a dedicated shared buffer with RPMsg used only for control messages.

### 9.1 Linux + RT-Thread configuration

Linux Kernel configuration:

```text
CONFIG_MAILBOX=y
CONFIG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_VIRTIO=y
CONFIG_RPMSG_TTY=y                 # Optional: create an RPMsg TTY
CONFIG_RPMSG_ROCKCHIP_TEST=y       # Optional: enable the test driver
```

Enable the following RT-Thread options through `scons --menuconfig`:

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_LINUX_RPMSG=y
CONFIG_RT_USING_COMMON_TEST_LINUX_RPMSG_LITE=y  # Optional test demo
```

### 9.2 Linux + bare-metal configuration

Enable Mailbox, Rockchip RPMsg, and VirtIO on the Linux side as described above. Enable the corresponding test in the HAL project:

```c
#define RPMSG_LINUX_TEST
```

The file containing this macro can change between SDK releases. Locate it with:

```bash
grep -R "RPMSG_LINUX_TEST" -n hal/project
```

### 9.3 RT-Thread + bare-metal configuration

Enable the following options in RT-Thread:

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_COMMON_TEST_RPMSG_LITE=y
```

The HAL test project normally requires:

```c
#define TEST_DEMO
#define TEST_USE_RPMSG_INIT
#define RPMSG_TEST
```

### 9.4 Verify Linux + Remote RPMsg

After Linux boots, check the RPMsg state:

```bash
dmesg | grep -Ei 'rpmsg|virtio|mailbox'
ls -l /sys/bus/rpmsg/devices/
ls -l /dev/ttyRPMSG* 2>/dev/null
```

A successful initialization produces messages similar to:

```text
rockchip-rpmsg ...: rockchip rpmsg platform probe
virtio_rpmsg_bus virtio0: rpmsg host is online
virtio_rpmsg_bus virtio0: creating channel rpmsg-ap3-ch0 ...
```

Typical remote-side messages are:

```text
rpmsg remote: remote core cpu_id-3
rpmsg remote: shmem_base-0x7c00000 shmem_end-8100000
rpmsg remote: link up! link_id-0x3
```

The Name Service string used by the Linux driver must match the name announced by the remote endpoint. For example, the Linux `rpmsg_tty` driver creates `/dev/ttyRPMSG*` only after the remote side announces `rpmsg-tty`.

## 10. Boot configurations

### 10.1 Linux + AP RTOS/bare metal

U-Boot runs on CPU0, loads the specified remote-core firmware from `amp.img`, releases the target CPU, and then continues to boot Linux. Linux starts only the CPUs assigned to its SMP domain.

For example, on a four-core SoC where CPU3 runs RT-Thread:

```text
BootROM/Loader → U-Boot@CPU0 → RT-Thread@CPU3
                         └──→ Linux@CPU0/CPU1/CPU2
```

### 10.2 Linux + MCU RTOS/bare metal

U-Boot loads and releases the MCU before continuing to boot Linux on the AP. The MCU node `type` must match the platform U-Boot implementation. Rockchip examples normally use `standalone`.

### 10.3 RTOS + bare metal

U-Boot loads the firmware for each CPU according to `loadables`. When CPU0 is the boot core, U-Boot normally releases the other CPUs first and finally jumps to the CPU0 firmware.

### 10.4 Fast boot

Some SoCs can load MCU firmware earlier in the SPL stage, or use an SPI NOR + eMMC dual-storage design to reduce MCU startup time. These solutions require coordinated changes to `rkbin` and U-Boot and must use a configuration designed for the target SoC. Do not reuse such configurations across platforms.

## 11. Interrupt configuration

### 11.1 Cortex-A GIC

GIC interrupts are divided into:

- SGI: interrupt IDs 0 through 15, generated by software and private to each CPU.
- PPI: interrupt IDs 16 through 31, private to each CPU.
- SPI: interrupt IDs starting at 32, shared between CPUs.

AP RTOS/HAL firmware must consistently configure each peripheral interrupt's priority and target CPU:

```c
#define DEFAULT_IRQ_CPU 1

static struct GIC_AMP_IRQ_INIT_CFG irqsConfig[] = {
    GIC_AMP_IRQ_CFG_ROUTE(GPIO0_IRQn, 0xd0,
                          CPU_GET_AFFINITY(0, 0)),
    GIC_AMP_IRQ_CFG_ROUTE(0, 0,
                          CPU_GET_AFFINITY(DEFAULT_IRQ_CPU, 0)),
};
```

Interrupts not explicitly listed are routed to `DEFAULT_IRQ_CPU`. When multiple RTOS/HAL systems share one GIC routing table, each SPI must have exactly one intended handler.

### 11.2 Cortex-M NVIC and RISC-V IPIC

- Cortex-M uses NVIC. When there are more external interrupt sources than direct NVIC inputs, INTMUX is normally used.
- The RK3568 RISC-V core uses IPIC and can also receive peripheral interrupts through INTMUX.
- `HAL_Init()` normally initializes NVIC. For RISC-V projects, verify the `HAL_INTMUX_Init()` and IPIC initialization path.

The complete peripheral interrupt enable sequence is: route the interrupt to the target core → register the ISR → enable the interrupt controller input → enable the peripheral's own interrupt source.

## 12. Debugging and troubleshooting

### 12.1 Recommended debug sequence

1. Boot only RTOS/HAL and verify the entry address, linker address, and debug UART.
2. Add Linux without RPMsg and verify that both sides start reliably.
3. Check `/proc/iomem`, DTS reserved memory, and the linked address ranges of every ELF file.
4. Enable Mailbox/SoftIRQ and RPMsg, and run the SDK demo first.
5. Transfer actual peripherals and application code one resource type at a time.

Useful Linux-side checks:

```bash
cat /proc/iomem
cat /proc/interrupts
dmesg | grep -Ei 'amp|rpmsg|virtio|mailbox|reserved|failed|error'
ls /sys/firmware/devicetree/base/reserved-memory/
```

Inspect the entry point and segments of an RTOS/HAL ELF file:

```bash
<cross-readelf> -h <firmware>.elf
<cross-readelf> -l <firmware>.elf
<cross-nm> -n <firmware>.elf | head
```

### 12.2 Common issues

| Symptom | Common cause | Resolution |
| --- | --- | --- |
| U-Boot does not print `Brought up cpu` | U-Boot was not built with `rk-amp.config`, the `amp` partition is missing, or FIT validation failed | Check the U-Boot configuration, partition table, `amp.img`, and `loadables` |
| Remote core faults immediately after startup | ITS `load` does not match the linked address, or the firmware exceeds its assigned region | Compare the ITS, map file, ELF program headers, and DTS |
| Linux crashes intermittently or data is corrupted | Remote-core memory is not reserved in Linux or overlaps CMA/another reserved region | Check `reserved-memory`, `/proc/iomem`, and the actual DDR capacity |
| RTOS/HAL has no serial output | UART is owned by Linux, the IOMUX group is wrong, the clock is not retained, or the baud rate differs | Disable the Linux UART node and check AMP clocks/pinctrl and board IOMUX |
| A peripheral works in polling mode but not with interrupts | GIC/NVIC/IPIC routing is wrong, or the peripheral interrupt source is disabled | Check the interrupt ID, target CPU, ISR, and peripheral registers in sequence |
| RPMsg only reports `host is online` | The remote is not running, shared addresses differ, or Name Service was not announced | Check remote logs, vring addresses, link ID, and endpoint name |
| RPMsg occasionally loses messages or hangs | Cache attributes differ, shared memory overlaps another region, or memory barriers are missing | Restore the SDK's default uncached configuration and check MMU/MPU mappings |
| `/dev/ttyRPMSG*` is missing | `CONFIG_RPMSG_TTY` is disabled or the remote did not announce `rpmsg-tty` | Check the Kernel configuration and remote Name Service |
| A newly assigned peripheral fails in both Linux and RTOS | Both sides still manage the peripheral, pinctrl, reset, or clock | Establish a single owner, disable the Linux DTS device, and retain required resources through the AMP node |

### 12.3 JTAG debugging

- Cortex-A RTOS/HAL firmware can be debugged with OpenOCD and JTAG by loading the ELF symbols for the corresponding CPU.
- A Cortex-M MCU can be debugged with J-Link/Ozone using connection scripts and address mappings designed for the target SoC.
- The ELF loaded into the debugger must come from the same build as the `amp.img` currently running on the board.

## 13. Development recommendations

- Copy an existing AMP board configuration from the SDK instead of starting from empty DTS and ITS files.
- Run the supplied demo with the default memory and RPMsg configuration before making product-specific changes.
- Maintain one address allocation table covering Linux, every Remote Core, and all shared-memory regions.
- Treat a peripheral's clock, reset, pinctrl, power domain, and IRQ as one resource bundle when transferring ownership.
- Use RPMsg for control and small-to-medium messages. Use a dedicated shared buffer for high-bandwidth data.
- Remote firmware should implement its own watchdog, timeout handling, error states, and safe degradation behavior.
- Before production, perform repeated cold boots, warm reboots, high-load runs, long-duration RPMsg tests, and peripheral interrupt stress tests.

## 14. Terminology

| Term | Description |
| --- | --- |
| AMP | Asymmetric Multi-Processing |
| SMP | Symmetric Multi-Processing |
| AP | Application Processor, normally a Cortex-A core |
| MCU | Microcontroller Unit, such as a Cortex-M or RISC-V core |
| RTOS | Real-Time Operating System |
| RTT | RT-Thread in this document |
| HAL | Hardware Abstraction Layer |
| FIT/ITS | U-Boot Flattened Image Tree and its source description file |
| RPMsg | Remote Processor Messaging, an inter-core messaging protocol |
| RPMsg-Lite | A lightweight RPMsg implementation for RTOS and bare-metal systems |
| vring | The ring queue used by VirtIO |
| Master Core | The core that manages shared resources and communication memory |
| Remote Core | A core loaded or managed by the Master Core |
