# EtherCAT Rockchip Real-Time Kernel

## 8. Rockchip Platform Support

### 8.4 Configuring the Real-Time Kernel

#### Applying PREEMPT_RT

First apply the PREEMPT_RT patches according to the SDK documentation:

```text
SDK/docs/Patches/Real-Time-Performance/README.md
```

You can also refer to the [RTLinux Real-Time System Development Guide](../RT-Linux/rtlinux_preempt_rt.md).

#### Building `stmmac` as a Module

The IgH platform driver must take control of the GMAC, so the standard `stmmac` driver cannot remain built into the kernel. Linux 5.10 and 6.1 generally require:

```text
CONFIG_STMMAC_ETH=m
```

Check the final configuration:

```bash
grep CONFIG_STMMAC_ETH .config
```

If the standard `stmmac` driver is built in and has already claimed the interface, `ec_stmmac.ko` cannot take control when loaded later.

#### CPU Isolation and Tick Configuration

The reference solution reserves one CPU for real-time tasks and enables full tick isolation:

```text
CONFIG_NO_HZ_FULL=y
```

Example kernel command-line parameters:

```text
isolcpus=<cpu> nohz_full=<cpu>
```

CPU3 is recommended for RK3568, and CPU7 for RK3576. For a real product, select the CPU again according to the CPU topology, IRQs, and application load instead of copying these CPU numbers directly.

If necessary, disable deep sleep for the isolated CPU or disable the relevant CPU Idle features in the kernel configuration. This increases power consumption and temperature and must be evaluated through extended testing.
