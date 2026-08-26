# EtherCAT Real-Time Optimization

## 7. Real-Time Optimization and Debugging

### 7.1 PREEMPT_RT

For applications with strict real-time requirements, such as motion control, a PREEMPT_RT kernel is recommended. For real-time kernel configuration in this repository, see the [RTLinux Real-Time System Development Guide](../RT-Linux/rtlinux_preempt_rt.md).

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
