# EtherCAT 实时性优化

## 7. 实时性优化与调试

### 7.1 PREEMPT_RT

对运动控制等实时性要求较高的业务，建议使用 PREEMPT_RT 内核。仓库内的实时内核配置方法参见 [RTLinux 实时系统开发指南](../RT-Linux/rtlinux_preempt_rt.md)。

实时内核只能降低调度延迟，不能替代正确的线程设计、网卡驱动、CPU 隔离和设备配置。

### 7.2 CPU 和调度优化

常见优化措施：

1. 将周期线程绑定到独立 CPU。
2. 将该 CPU 从普通任务和负载均衡中隔离。
3. 将相关网卡 IRQ 绑定到规划好的 CPU。
4. 使用 `SCHED_FIFO`，并为线程选择合理优先级。
5. 将 CPU 调频策略设为 `performance`。
6. 评估是否需要关闭深度 CPU Idle 状态。
7. 避免实时 CPU 上运行图形界面、日志压缩和高负载服务。

查看线程和 IRQ：

```bash
ps -eLo pid,tid,cls,rtprio,psr,comm
cat /proc/interrupts
taskset -pc <pid>
```

设置 CPU 亲和性时应根据 SoC 拓扑、IRQ 分布和业务负载选择 CPU，不能直接复制其他平台的核编号。

### 7.3 性能模式

设置 CPU 调频策略：

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

产品部署时应通过系统服务固定配置，并评估功耗与散热。
