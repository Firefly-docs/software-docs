# EtherCAT Cyclic Application

## 6. Developing IgH Applications

### 6.3 Basic Structure of a Cyclic Task

The following simplified call sequence illustrates how the master API is used:

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

When using DC, set the application time and synchronize the clocks at the correct point in the cycle:

```c
ecrt_master_application_time(master, app_time_ns);
ecrt_master_sync_reference_clock_to(master, app_time_ns);
ecrt_master_sync_slave_clocks(master);
```

The specific synchronization method depends on the reference clock, slave capabilities, and control period. Refer to the IgH API documentation and slave manual.

### 6.4 Building an Application

If IgH is installed in `/opt/etherlab`:

```bash
gcc main.c -o ethercat_app \
  -I/opt/etherlab/include \
  -L/opt/etherlab/lib \
  -Wl,-rpath,/opt/etherlab/lib \
  -lethercat -lpthread
```

For cross-compilation, use the target platform compiler and ensure that the headers and `libethercat.so` come from the same IgH build.
