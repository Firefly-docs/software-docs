# EtherCAT 周期、抖动和抓包

## 7. 实时性优化与调试

### 7.4 周期和抖动测量

如果目标周期为 `T`，可以记录每次实际唤醒时间，计算：

```text
period  = current_start - previous_start
latency = current_start - target_wakeup
jitter  = maximum_period - minimum_period
```

测试时应记录：

- 测试周期和持续时间。
- 最大、最小和分位延迟，而不只记录平均值。
- 从站数量、PDO 大小和 DC 配置。
- CPU 绑定、IRQ 绑定、调频策略和系统负载。
- 是否使用专用网卡驱动、TSN 或硬件时间戳。

可先使用 `cyclictest` 评估系统调度延迟，再运行真实 EtherCAT 应用评估端到端周期。

### 7.5 抓包

可以使用 Wireshark 或 `tcpdump` 检查 EtherCAT 帧：

```bash
sudo tcpdump -i eth0 -e -n ether proto 0x88a4
```

抓包会引入额外负载，只适合诊断，不应长期运行在实时系统中。使用专用驱动时，普通网络抓包接口不一定能观察到全部报文。
