# EtherCAT IgH Master Usage and Platform Support

## 1. Overview

### 1.1 Introduction to EtherCAT

EtherCAT (Ethernet for Control Automation Technology) is a real-time Ethernet technology for industrial automation. It uses the standard Ethernet physical layer, but slaves typically read output data and write input data directly as a frame passes through, reducing the latency introduced by forwarding frames from one slave to the next.

EtherCAT is commonly used in the following scenarios:

- Servo drives and multi-axis motion control.
- Industrial I/O, sensors, and actuators.
- Robots, CNC equipment, and automated production lines.
- Measurement and control systems that require distributed clock synchronization.

A typical topology is shown below:

```text
Linux EtherCAT master
        │
        ├── Slave 0: Servo drive / I/O
        │
        ├── Slave 1: Servo drive / I/O
        │
        └── Slave N: Sensor / actuator
```

An EtherCAT network should use a dedicated Ethernet interface. The master directly transmits and receives Layer 2 EtherCAT frames through this interface. The interface generally has no IP address and should not be managed simultaneously by NetworkManager, a DHCP client, or standard network services.

### 1.2 Introduction to the IgH EtherCAT Master

The IgH EtherCAT Master is an open-source Linux EtherCAT master implementation provided by the EtherLab project. Its core runs in kernel space, while user applications communicate with the master through a character device and `libethercat`.

The IgH master mainly consists of the following components:

| Component | Common file or interface | Function |
| --- | --- | --- |
| Master kernel module | `ec_master.ko` | Manages the EtherCAT bus, slave states, process data, and clock synchronization |
| Network driver | `ec_generic.ko` or a dedicated driver | Connects the master to the physical Ethernet interface; dedicated drivers generally provide better real-time performance |
| Character device | `/dev/EtherCAT0` | Interface between the kernel master and user-space tools and applications |
| User-space library | `libethercat.so` | Provides the `ecrt_*` application development API |
| Command-line tool | `ethercat` | Scans slaves, displays PDOs, reads and writes SDOs, changes states, and diagnoses the bus |
| Master application | User application | Performs cyclic communication, state-machine processing, and application control according to the device configuration |

```text
EtherCAT application / ethercat tool
                  │
                  ▼
            libethercat.so
                  │
                  ▼
            /dev/EtherCAT0
                  │
                  ▼
             ec_master.ko
                  │
                  ▼
 Generic NIC driver or platform real-time NIC driver
                  │
                  ▼
             EtherCAT slaves
```

Official IgH API documentation:

- [EtherLab EtherCAT Master API](https://docs.etherlab.org/ethercat/1.5/doxygen/index.html)
- [EtherLab EtherCAT source code](https://gitlab.com/etherlab.org/ethercat)

### 1.3 Basic Terminology

| Term | Meaning |
| --- | --- |
| Master | EtherCAT master responsible for discovering, configuring, and cyclically accessing slaves |
| Slave | EtherCAT slave, such as a servo drive, I/O module, or sensor |
| PDO | Process Data Object, real-time process data exchanged cyclically |
| SDO | Service Data Object, generally used for acyclic parameter access and device configuration |
| CoE | CAN application protocol over EtherCAT, a commonly used object dictionary protocol |
| DC | Distributed Clocks, the EtherCAT distributed clock synchronization mechanism |
| ESI | EtherCAT Slave Information, an XML description file provided by the slave vendor |
| Alias | Slave alias address used to identify a device consistently |
| Position | Physical position of a slave in the bus topology, starting at `0` |

EtherCAT slaves generally transition through the following states:

```text
INIT → PREOP → SAFEOP → OP
```

- `INIT`: Initialization state; mailbox and process data communication have not started.
- `PREOP`: Mailbox communication and SDO configuration are available, but valid PDO data is not yet exchanged.
- `SAFEOP`: Input process data is valid, while outputs generally remain in a safe state.
- `OP`: Input and output process data is exchanged normally.

## 2. Preparation

### 2.1 Hardware Requirements

At least the following equipment is required:

1. A master device running Linux with a compatible, dedicated Ethernet interface.
2. One or more EtherCAT slaves.
3. Ethernet cables, power supplies, power cables, and encoder cables that meet the device requirements.
4. The slave manuals and ESI XML files.
5. Emergency stops, limit switches, and mechanical safeguards required for servo system commissioning.

EtherCAT slaves should be connected in series according to the port direction. Some devices clearly distinguish between `IN` and `OUT` ports; reversing them may prevent the master from scanning the bus successfully.

### 2.2 Software Requirements

The following software environment is recommended:

- Linux kernel source and a cross-compilation toolchain matching the target board.
- The IgH EtherCAT Master source code or a prebuilt package provided by the platform SDK.
- `make`, Autoconf, Automake, Libtool, Pkg-config, and a C compiler.
- A PREEMPT_RT kernel for applications with strict real-time requirements.
- Tools such as `phc2sys` and `ptp4l` from `linuxptp` when using DC, TSN, or hardware timestamps.

### 2.3 Safety Information

Normal EtherCAT communication does not mean that a motor can be operated safely. Observe the following precautions when commissioning servo drives:

- Disconnect power output or raise the mechanical load before the first test.
- Verify the emergency stop, limit switch, and drive alarm logic before enabling the motor.
- Check the control word, operating mode, target position, target velocity, and unit conversions.
- When the application exits, the link is disconnected, or the master enters an error state, write a safe control word and stop the outputs.
- Do not apply the target velocity, position, or torque values from an example directly to real equipment.

## 3. Building and Installing the IgH Master

This chapter describes the generic IgH build procedure. When using a customized package from the Rockchip SDK, refer first to [Chapter 8](#8-rockchip-platform-support), because its master and `stmmac` driver include platform-specific adaptations.

### 3.1 Obtaining the Source Code

```bash
git clone https://gitlab.com/etherlab.org/ethercat.git
cd ethercat
```

For product development, pin the source to a verified tag or commit instead of depending directly on the continuously changing `master` branch.

### 3.2 Configuring Build Options

First generate `configure`:

```bash
./bootstrap
```

Native build example:

```bash
./configure \
  --prefix=/opt/etherlab \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-generic=yes
```

Cross-compilation example:

```bash
export ARCH=arm64
export CROSS_COMPILE=aarch64-none-linux-gnu-

./configure \
  --host=aarch64-none-linux-gnu \
  --prefix=/opt/etherlab \
  --with-linux-dir=/path/to/kernel \
  --enable-generic=yes
```

Common options:

| Option | Description |
| --- | --- |
| `--with-linux-dir=<path>` | Specifies the configured target kernel source directory |
| `--enable-generic=yes` | Builds the generic network driver `ec_generic` |
| `--enable-<driver>=yes` | Builds the specified native network driver supported by IgH |
| `--enable-wildcards=yes` | Allows wildcard matching in master device configuration; a fixed interface is still recommended for products |
| `--prefix=<path>` | Specifies the installation path for user-space libraries and tools |

Available driver options vary by IgH version. Check the output from the current source tree:

```bash
./configure --help | less
```

### 3.3 Building and Installing

```bash
make -j$(nproc) all modules
sudo make modules_install install
sudo depmod -a
```

For cross-compilation, you can first install the files into a staging directory:

```bash
make -j$(nproc) all modules
make DESTDIR=$PWD/output modules_install install
```

Then deploy the kernel modules, shared libraries, headers, tools, and service files under `output` to the corresponding directories on the target system.

### 3.4 Verifying the Installation

```bash
find /lib/modules/$(uname -r) -name 'ec_*.ko*'
command -v ethercat
ldconfig -p | grep libethercat
```

If `ethercat` runs but reports that it cannot open the master device, continue checking the kernel modules and `/dev/EtherCAT0` instead of checking only the user-space tool.

## 4. Master Configuration and Startup

### 4.1 Selecting the EtherCAT Interface

Display network interfaces and MAC addresses:

```bash
ip -br link
ip link show eth0
```

Select an interface dedicated to EtherCAT and record its permanent MAC address. Do not use a MAC address that changes randomly.

If a system service manages the interface, make it unmanaged first. The configuration method differs by distribution. For example, with NetworkManager, use:

```bash
nmcli device set eth0 managed no
```

### 4.2 Loading the Generic IgH Driver

Generic driver example:

```bash
sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_generic
```

Replace the example MAC address with the actual address of the dedicated EtherCAT interface. Module parameter names and configuration file paths may vary between distribution packages. Confirm them with:

```bash
modinfo ec_master
modinfo ec_generic
```

Some installation methods provide `/etc/ethercat.conf` or `/etc/sysconfig/ethercat`, where the master interface and driver can be configured:

```text
MASTER0_DEVICE="62:36:B8:01:5B:59"
DEVICE_MODULES="generic"
```

Follow the format required by the service script included with the installed package.

### 4.3 Verifying the Master

```bash
ls -l /dev/EtherCAT*
sudo ethercat master
sudo ethercat slaves
dmesg | grep -i ethercat
```

Normally, the master, link state, and slave list should be displayed. If no slaves are found:

1. Check the Ethernet cable direction, slave power, and link indicators.
2. Confirm that `main_devices` contains the correct MAC address.
3. When using `ec_generic`, confirm that the standard network driver works correctly and that normal network services are not sending or receiving IP traffic through the interface.
4. Check the actual device bound after the master is loaded.
5. Start testing with a single slave, then gradually restore the complete topology.

### 4.4 Stopping the Master

Stop the application and ensure that outputs have entered a safe state before unloading the drivers:

```bash
sudo modprobe -r ec_generic
sudo modprobe -r ec_master
```

When using a platform-specific driver, unload modules in the reverse order in which they were loaded.

## 5. Using the `ethercat` Tool

### 5.1 Displaying Help

```bash
ethercat --help
ethercat slaves --help
ethercat pdos --help
```

Most commands support the following selection options:

- `-m <master>`: Select a master by number.
- `-p <position>`: Select a slave by physical position.
- `-a <alias>`: Select a slave by alias address.

Refer to `ethercat <command> --help` on the target system for the exact options.

### 5.2 Displaying Masters and Slaves

```bash
ethercat master
ethercat slaves
```

Typical slave output contains the position, alias, state, and device name:

```text
0  0:0  PREOP  +  Servo Drive
```

Display detailed information:

```bash
ethercat slaves -v
ethercat config
```

### 5.3 Setting an Alias Address

Set the alias of the slave at position `0` to `1`:

```bash
ethercat alias -p 0 1
```

The alias is written to the slave EEPROM. Confirm the target position before running the command to avoid modifying the wrong device.

### 5.4 Displaying PDOs

```bash
ethercat pdos -p 0
ethercat cstruct -p 0
```

`ethercat pdos` displays the slave's current PDO assignment. `ethercat cstruct` generates PDO entries and Sync Manager structures that can be used as a reference by a C application.

The automatically generated structures reflect only the current device configuration. Check the indexes, subindexes, bit lengths, directions, and Sync Manager numbers against the slave manual.

### 5.5 Displaying, Reading, and Writing SDOs

List the object dictionary:

```bash
ethercat sdos -p 0
```

Read object `0x1018:01`:

```bash
ethercat upload -p 0 0x1018 1
```

Read with a specified data type:

```bash
ethercat upload -p 0 --type uint32 0x1018 1
```

Before writing an SDO, verify the device manual, data type, and units. For example:

```bash
ethercat download -p 0 --type uint16 0x2000 0 1
```

An incorrect SDO write may change motor parameters or persistent configuration. Back up parameters before working on production equipment, and do not modify critical objects while a motor is running.

### 5.6 Changing States and Rescanning

```bash
ethercat states PREOP
ethercat states SAFEOP
ethercat states OP
ethercat rescan
```

If a state transition fails, check slave errors and master logs first. Do not repeatedly force the slaves into `OP`.

### 5.7 Exporting Slave Information

```bash
ethercat xml -p 0 > slave.xml
ethercat sii_read -p 0 > slave-sii.bin
```

Commands may differ between IgH versions. Writing SII/EEPROM data is high risk. Unless you fully understand the slave data format, only read and back up the data.

## 6. Developing IgH Applications

### 6.1 Development Workflow

An IgH application is generally initialized in the following order:

1. Obtain the master with `ecrt_request_master()`.
2. Create a process data domain with `ecrt_master_create_domain()`.
3. Configure slaves by alias, position, Vendor ID, and Product Code with `ecrt_master_slave_config()`.
4. Configure Sync Managers and PDOs with `ecrt_slave_config_pdos()`.
5. Register the PDO entries accessed by the application with `ecrt_domain_reg_pdo_entry_list()`.
6. Configure DC, SDOs, watchdogs, and slave-specific parameters as needed.
7. Activate the master with `ecrt_master_activate()`.
8. Obtain the process data memory with `ecrt_domain_data()`.
9. Enter the fixed-period real-time loop.
10. Before exiting, stop outputs, release the master, and restore a safe state.

```text
Request master → Create domain → Configure slaves and PDOs → Activate master
                                                   │
                                                   ▼
Receive → Process domain → Read inputs → Write outputs → Queue → Send
   ▲                                                               │
   └──────────────────── Fixed-period loop ─────────────────────────┘
```

### 6.2 Generating a PDO Configuration Reference

Connect and power on the slaves, then run:

```bash
ethercat slaves
ethercat pdos -p 0
ethercat cstruct -p 0
```

Use the `cstruct` output as a starting point for the application configuration. Remove unnecessary entries or adjust the PDO mapping according to the slave manual. Confirm the Vendor ID and Product Code with:

```bash
ethercat slaves -v
ethercat xml -p 0
```

Do not match slaves only by device name. The application should validate the Vendor ID and Product Code and, when necessary, the Revision Number.

### 6.3 Basic Structure of a Cyclic Task

The following simplified call sequence illustrates how the master API is used:

```c
while (running) {
    clock_nanosleep(CLOCK_MONOTONIC, TIMER_ABSTIME,
                    &wakeup_time, NULL);

    ecrt_master_receive(master);
    ecrt_domain_process(domain);

    /* Read inputs from domain_pd with EC_READ_*(). */
    /* Run the device state machine and control algorithm. */
    /* Write outputs to domain_pd with EC_WRITE_*(). */

    ecrt_domain_queue(domain);
    ecrt_master_send(master);
}
```

When using DC, set the application time and synchronize the clocks at the correct point in the cycle:

```c
ecrt_master_application_time(master, app_time_ns);
ecrt_master_sync_reference_clock_to(master, app_time_ns);
ecrt_master_sync_slave_clocks(master);
```

The specific synchronization method depends on the reference clock, slave capabilities, and control period. Refer to the IgH API documentation and slave manual.

### 6.4 Building an Application

If IgH is installed in `/opt/etherlab`:

```bash
gcc main.c -o ethercat_app \
  -I/opt/etherlab/include \
  -L/opt/etherlab/lib \
  -Wl,-rpath,/opt/etherlab/lib \
  -lethercat -lpthread
```

For cross-compilation, use the target platform compiler and ensure that the headers and `libethercat.so` come from the same IgH build.

### 6.5 Real-Time Thread Configuration

Real-time applications generally need to lock memory and increase the scheduling priority of the cyclic thread:

```c
struct sched_param param = {
    .sched_priority = 80,
};

mlockall(MCL_CURRENT | MCL_FUTURE);
sched_setscheduler(0, SCHED_FIFO, &param);
```

The following practices are also recommended:

- Use absolute-time sleeps to prevent accumulated period drift.
- Complete memory allocation, file opening, and device initialization before entering the real-time loop.
- Avoid frequent `printf()` calls, dynamic memory allocation, or blocking I/O in the cyclic thread.
- Run logging, UI, and network services in non-real-time threads.
- Access only preallocated and correctly synchronized data from the cyclic thread.

Increasing the scheduling priority requires the appropriate permissions. Run as root, or configure the system to grant `CAP_SYS_NICE` and permission to lock memory.

### 6.6 CiA 402 Servo State Machine

Many EtherCAT servo drives use the CoE CiA 402 state machine. A common control sequence is:

```text
Fault Reset
    ↓
Shutdown (0x0006)
    ↓
Switch On (0x0007)
    ↓
Enable Operation (0x000F)
    ↓
Write target position / velocity / torque
```

Actual control-word decisions must use the status-word mask and the drive manual. Requirements for PDOs, units, enable sequences, and stopping behavior may differ between operating modes.

### 6.7 State Monitoring

The application should check the following states periodically or when events occur:

- `ec_master_state_t`: Master link, number of slaves, and AL state.
- `ec_domain_state_t`: Domain working counter and working counter state.
- `ec_slave_config_state_t`: Whether the slave is online and operational.
- Slave status word, error code, and vendor-specific diagnostic objects.

An incomplete working counter generally indicates that a slave is offline, the PDO configurations do not match, or some datagrams did not receive responses. Do not continue outputting hazardous control values while the working counter is abnormal.

## 7. Real-Time Optimization and Debugging

### 7.1 PREEMPT_RT

For applications with strict real-time requirements, such as motion control, a PREEMPT_RT kernel is recommended. For real-time kernel configuration in this repository, see the [RTLinux Real-Time System Development Guide](../RT-Linux/rtlinux.md).

A real-time kernel can reduce scheduling latency, but it cannot replace proper thread design, network drivers, CPU isolation, and device configuration.

### 7.2 CPU and Scheduling Optimization

Common optimization measures include:

1. Bind the cyclic thread to a dedicated CPU.
2. Isolate that CPU from normal tasks and load balancing.
3. Bind the relevant network interface IRQs to planned CPUs.
4. Use `SCHED_FIFO` and select a reasonable thread priority.
5. Set the CPU frequency governor to `performance`.
6. Evaluate whether deep CPU Idle states need to be disabled.
7. Avoid running graphical interfaces, log compression, and high-load services on a real-time CPU.

Display threads and IRQs:

```bash
ps -eLo pid,tid,cls,rtprio,psr,comm
cat /proc/interrupts
taskset -pc <pid>
```

When setting CPU affinity, select CPUs according to the SoC topology, IRQ distribution, and application load. Do not copy CPU numbers directly from another platform.

### 7.3 Performance Mode

Set the CPU frequency governor:

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

For product deployment, make the setting persistent through a system service and evaluate power consumption and thermal performance.

### 7.4 Measuring Period and Jitter

If the target period is `T`, record the actual wake-up time for each cycle and calculate:

```text
period  = current_start - previous_start
latency = current_start - target_wakeup
jitter  = maximum_period - minimum_period
```

Record the following during testing:

- Test period and duration.
- Maximum, minimum, and percentile latency, rather than only the average.
- Number of slaves, PDO size, and DC configuration.
- CPU binding, IRQ binding, frequency governor, and system load.
- Whether a dedicated network driver, TSN, or hardware timestamps are used.

First use `cyclictest` to evaluate system scheduling latency, then run the actual EtherCAT application to evaluate the end-to-end cycle.

### 7.5 Packet Capture

Use Wireshark or `tcpdump` to inspect EtherCAT frames:

```bash
sudo tcpdump -i eth0 -e -n ether proto 0x88a4
```

Packet capture adds load and should be used only for diagnostics, not run continuously on a real-time system. When a dedicated driver is used, a standard network capture interface may not observe every frame.

### 7.6 Common Problems

#### `/dev/EtherCAT0` Cannot Be Found

Check:

```bash
lsmod | grep '^ec_'
dmesg | grep -i ethercat
modinfo ec_master
```

Confirm that the master module exactly matches the running kernel version, and check whether the device-node rules or master service ran correctly.

#### The Master Exists but Cannot Scan Any Slaves

- Check the MAC address and the interface bound to the master.
- Check slave power, IN/OUT direction, and Ethernet cables.
- When using `ec_generic`, confirm that the standard network driver works and normal network services do not use the interface. When using a native or platform-specific driver, confirm that the standard network driver does not occupy the device.
- Check the link speed; EtherCAT devices generally use 100 Mbit/s full duplex.
- Start troubleshooting with one slave and one short Ethernet cable.

#### A Slave Remains in PREOP

- Use `ethercat slaves -v` to display the AL state and error code.
- Check the PDO assignment and Sync Manager configuration.
- Check whether SDO initialization succeeded.
- Verify the Vendor ID, Product Code, and Revision Number.

#### A Slave Remains in SAFEOP

- Check whether the application has started cyclic communication.
- Check whether the working counter is complete.
- Check the watchdog, cycle period, DC, and Sync0/Sync1 configuration.
- Check whether output PDOs are initialized as required by the device.

#### Cycle Jitter Is High

- Confirm that PREEMPT_RT and a suitable real-time network driver are used.
- Check whether other tasks or IRQs preempt the real-time thread.
- Check CPU frequency scaling, thermal throttling, and deep sleep states.
- Remove logging, memory allocation, and blocking calls from the cyclic thread.
- Test the system with no load, one slave, and the complete topology separately.

#### `ethercat` Cannot Load `libethercat.so`

```bash
ldd "$(command -v ethercat)"
ldconfig -p | grep libethercat
```

Install the library in a system library directory and run `ldconfig`, or configure the runtime library search path correctly. Do not mix tools, libraries, headers, and kernel modules from different IgH versions.

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

### 8.4 Configuring the Real-Time Kernel

#### Applying PREEMPT_RT

First apply the PREEMPT_RT patches according to the SDK documentation:

```text
SDK/docs/Patches/Real-Time-Performance/README.md
```

You can also refer to the [RTLinux Real-Time System Development Guide](../RT-Linux/rtlinux.md).

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

### 8.5 Building

Build directly on the target machine:

```bash
./bootstrap

# Use --prefix to specify the installation directory for user-space programs
./configure \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-8139too=no \
  --enable-stmmac=yes \
  --enable-generic=no \
  --enable-wildcards=yes

```

Build and install the files:

```bash
make -j8 all modules
make modules_install install
```

Use `modprobe` and module dependency management:

```bash
sudo depmod -a

sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_stmmac
```

Replace `main_devices` with the actual MAC address of the EtherCAT GMAC:

```bash
ip link
cat /sys/class/net/eth0/address
```

After loading the modules, check:

```bash
dmesg | tail -n 100
ls -l /dev/EtherCAT0
ethercat master
ethercat slaves
```

If `ecdev` for the platform `stmmac` driver is `0x0` in the log, the most common cause is an incorrect MAC address in `main_devices`. Also check whether the target GMAC has been claimed by the standard driver and whether the correct interface is selected in the device tree.

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

### 8.8 Performance Reference

The reference documentation provides the following platform data for a `1 ms` control period:

| SoC | Control period | Normal-mode jitter | TSN-mode jitter |
| --- | --- | --- | --- |
| RK3588 | 1 ms | 15 µs | 1 µs |
| RK3576 | 1 ms | 30 µs | 1 µs |
| RK3568 | 1 ms | 50 µs | N/A |
| RK3506 | 1 ms | 100 µs | N/A |

These figures only indicate the order of magnitude of the reference solution and are not guaranteed for every development board and system. Actual results are affected by:

- SDK, kernel, PREEMPT_RT, and driver versions.
- CPU isolation, IRQ affinity, frequency scaling, and thermal policies.
- PHY, Ethernet cables, number of slaves, PDO size, and topology.
- Application thread design, logging, UI, and background system load.
- The jitter calculation method and whether the actual hardware transmission time is measured.

Product acceptance testing should run for an extended period on the target hardware, final system image, and maximum application load, and should record worst-case and high-percentile values.

### 8.9 Production Recommendations

- Pin the IgH, kernel, real-time patch, and platform driver commits.
- Fix the EtherCAT interface, MAC address, module loading order, and CPU/IRQ affinity in the product configuration.
- The startup service should wait for the modules, device node, and slaves to become ready and provide failure fallback handling.
- Force outputs into a safe state when the application exits, the watchdog times out, or the link is disconnected.
- Preserve slave ESI files, PDO/SDO configurations, firmware versions, and parameter backups.
- Establish no-load, full-load, extended stability, high/low-temperature, and disconnection recovery tests.
