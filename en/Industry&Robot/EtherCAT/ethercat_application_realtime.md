# EtherCAT Real-Time Application Threads

## 6. Developing IgH Applications

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
