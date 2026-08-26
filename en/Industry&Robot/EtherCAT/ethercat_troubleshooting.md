# EtherCAT Troubleshooting

## 7. Real-Time Optimization and Debugging

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
