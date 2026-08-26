# EtherCAT 实时应用线程

## 6. IgH 应用开发

### 6.5 实时线程设置

实时应用通常需要锁定内存并提高周期线程调度优先级：

```c
struct sched_param param = {
    .sched_priority = 80,
};

mlockall(MCL_CURRENT | MCL_FUTURE);
sched_setscheduler(0, SCHED_FIFO, &param);
```

同时建议：

- 使用绝对时间睡眠，避免周期误差累积。
- 在进入实时循环前完成内存分配、文件打开和设备初始化。
- 避免在周期线程中频繁调用 `printf()`、动态分配内存或执行阻塞 I/O。
- 将日志、UI 和网络服务放在非实时线程。
- 周期线程只访问预先分配并正确同步的数据。

提高调度优先级需要相应权限。可以使用 root 运行，或通过系统权限配置授予 `CAP_SYS_NICE` 和锁定内存的能力。
