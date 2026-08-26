# EtherCAT 周期应用

## 6. IgH 应用开发

### 6.3 周期任务基本结构

下面是简化后的调用顺序，用于说明主站 API 的使用关系：

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

使用 DC 时，还需要在正确的时序位置设置应用时间并同步时钟：

```c
ecrt_master_application_time(master, app_time_ns);
ecrt_master_sync_reference_clock_to(master, app_time_ns);
ecrt_master_sync_slave_clocks(master);
```

具体同步方式取决于参考时钟、从站能力和控制周期，应以 IgH API 文档及从站手册为准。

### 6.4 编译应用

如果 IgH 安装在 `/opt/etherlab`：

```bash
gcc main.c -o ethercat_app \
  -I/opt/etherlab/include \
  -L/opt/etherlab/lib \
  -Wl,-rpath,/opt/etherlab/lib \
  -lethercat -lpthread
```

交叉编译时使用目标平台编译器，并确保头文件和 `libethercat.so` 来自同一套 IgH 构建结果。
