# EtherCAT Overview

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
