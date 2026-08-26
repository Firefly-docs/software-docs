# EtherCAT Rockchip Platform Support

## 8. Rockchip Platform Support

### 8.1 Supported Platforms

The following platforms are supported:

| SoC | Reference kernel | IgH master | Rockchip real-time `stmmac` | TSN extension |
| --- | --- | --- | --- | --- |
| RK3588 | Linux 5.10 | Supported | Supported | Supported |
| RK3576 | Linux 6.1 | Supported | Supported | Supported |
| RK3568 | Linux 5.10 | Supported | Supported | Not supported |
| RK3506 | Linux 6.1 | Supported | Supported | Not supported |

Whether a particular development board exposes a compatible GMAC, which PHY it uses, its device-tree configuration, and whether its SDK contains the required patches must still be confirmed for each product.

### 8.2 Rockchip Software Architecture

The Rockchip solution consists of four layers: the real-time kernel, master and drivers, user space, and the application. The relevant files are listed below:

| Part | File | Function |
| --- | --- | --- |
| Real-time kernel | PREEMPT_RT kernel | Reduces scheduling and interrupt latency |
| IgH master | `ec_master.ko` | EtherCAT master core |
| Platform network driver | `ec_stmmac.ko` | Rockchip `stmmac` driver adapted for GMAC real-time communication |
| Dependency modules | `phylink.ko`, `pcs-xpcs.ko` | Provide PHY Link, PCS/XPCS, and other network interface support |
| User space | `ethercat`, `libethercat.so` | Debugging tools and application API |
| Application | SDK demo or product application | Servo, I/O, and application control |

```text
Product application / ethercat tool
                 │
                 ▼
           libethercat.so
                 │
                 ▼
            ec_master.ko
                 │
                 ▼
            ec_stmmac.ko
                 │
                 ▼
 Rockchip GMAC / PHY → EtherCAT slaves
```

Compared with the generic `ec_generic`, `ec_stmmac` directly adapts the platform GMAC data path and is suitable for scenarios with stricter cycle and jitter requirements. The two drivers cannot occupy the same physical interface simultaneously.

### 8.3 SDK File Location

Source directory:

```
SDK/external/ethercat_igh
```
