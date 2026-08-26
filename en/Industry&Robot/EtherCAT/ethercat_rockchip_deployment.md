# Deploy a Rockchip EtherCAT System

## 8. Rockchip Platform Support

### 8.6 Starting a Real-Time Application

Set the CPU to performance mode:

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

Confirm the bus before starting the application:

```bash
ethercat slaves
ethercat pdos
ethercat cstruct
sudo ./ethercat_app
```

### 8.7 Dual-Interface Configuration

Products with two GMACs can dedicate one interface to EtherCAT and retain the other for standard Ethernet. For example:

```text
gmac0 → EtherCAT IgH
gmac1 → TCP/IP, SSH, and standard network services
```

The reference RK3568 device tree marks the EtherCAT GMAC with a dedicated compatible string:

```dts
gmac0: ethernet@fe2a0000 {
    compatible = "rockchip,rk3568-gmac-ethercat", "snps,dwmac-4.20a";
    reg = <0x0 0xfe2a0000 0x0 0x10000>;
};
```

When using two interfaces:

- `main_devices` must contain the MAC address of the EtherCAT interface, not the standard network interface.
- Do not configure an IP address on the EtherCAT interface or allow NetworkManager to manage it.
- Continue using the standard `stmmac` driver for the standard network interface.
- The device tree, clocks, resets, PHYs, and pinctrl for both GMACs must be configured correctly and independently.
- The dedicated compatible string and driver support depend on the corresponding SDK patches and cannot be used directly with an unadapted kernel.
