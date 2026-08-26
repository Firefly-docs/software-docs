# AMP RPMsg 平台配置

## 9. RPMsg 核间通信

### 9.2 Linux + Bare-metal 配置

Linux 侧同样开启 Mailbox、Rockchip RPMsg 和 VirtIO。HAL 侧在对应测试工程中开启：

```c
#define RPMSG_LINUX_TEST
```

实际宏所在文件可能随 SDK 版本变化，可搜索：

```bash
grep -R "RPMSG_LINUX_TEST" -n hal/project
```

### 9.3 RT-Thread + Bare-metal 配置

RT-Thread 侧开启：

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_COMMON_TEST_RPMSG_LITE=y
```

HAL 测试工程一般需开启：

```c
#define TEST_DEMO
#define TEST_USE_RPMSG_INIT
#define RPMSG_TEST
```

### 9.4 验证 Linux + Remote RPMsg

Linux 启动后检查：

```bash
dmesg | grep -Ei 'rpmsg|virtio|mailbox'
ls -l /sys/bus/rpmsg/devices/
ls -l /dev/ttyRPMSG* 2>/dev/null
```

正常初始化时可看到类似日志：

```text
rockchip-rpmsg ...: rockchip rpmsg platform probe
virtio_rpmsg_bus virtio0: rpmsg host is online
virtio_rpmsg_bus virtio0: creating channel rpmsg-ap3-ch0 ...
```

Remote 端的典型日志：

```text
rpmsg remote: remote core cpu_id-3
rpmsg remote: shmem_base-0x7c00000 shmem_end-8100000
rpmsg remote: link up! link_id-0x3
```

Linux 驱动和 Remote 端使用的 Name Service 名称必须一致。例如 Remote 端 announce `rpmsg-tty` 后，Linux `rpmsg_tty` 驱动才会创建 `/dev/ttyRPMSG*`。
