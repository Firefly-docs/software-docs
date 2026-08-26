# EtherCAT Period, Jitter, and Packet Capture

## 7. Real-Time Optimization and Debugging

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
