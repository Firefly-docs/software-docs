# AMP RPMsg Platform Configurations

## 9. RPMsg inter-core communication

### 9.2 Linux + bare-metal configuration

Enable Mailbox, Rockchip RPMsg, and VirtIO on the Linux side as described above. Enable the corresponding test in the HAL project:

```c
#define RPMSG_LINUX_TEST
```

The file containing this macro can change between SDK releases. Locate it with:

```bash
grep -R "RPMSG_LINUX_TEST" -n hal/project
```

### 9.3 RT-Thread + bare-metal configuration

Enable the following options in RT-Thread:

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_COMMON_TEST_RPMSG_LITE=y
```

The HAL test project normally requires:

```c
#define TEST_DEMO
#define TEST_USE_RPMSG_INIT
#define RPMSG_TEST
```

### 9.4 Verify Linux + Remote RPMsg

After Linux boots, check the RPMsg state:

```bash
dmesg | grep -Ei 'rpmsg|virtio|mailbox'
ls -l /sys/bus/rpmsg/devices/
ls -l /dev/ttyRPMSG* 2>/dev/null
```

A successful initialization produces messages similar to:

```text
rockchip-rpmsg ...: rockchip rpmsg platform probe
virtio_rpmsg_bus virtio0: rpmsg host is online
virtio_rpmsg_bus virtio0: creating channel rpmsg-ap3-ch0 ...
```

Typical remote-side messages are:

```text
rpmsg remote: remote core cpu_id-3
rpmsg remote: shmem_base-0x7c00000 shmem_end-8100000
rpmsg remote: link up! link_id-0x3
```

The Name Service string used by the Linux driver must match the name announced by the remote endpoint. For example, the Linux `rpmsg_tty` driver creates `/dev/ttyRPMSG*` only after the remote side announces `rpmsg-tty`.
