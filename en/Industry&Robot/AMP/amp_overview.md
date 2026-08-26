# AMP Overview

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
