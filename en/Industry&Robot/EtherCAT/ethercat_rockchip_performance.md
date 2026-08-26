# Rockchip EtherCAT Performance and Production

## 8. Rockchip Platform Support

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
