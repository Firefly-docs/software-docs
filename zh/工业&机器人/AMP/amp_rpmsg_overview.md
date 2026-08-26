# AMP RPMsg 概述

## 9. RPMsg 核间通信

Rockchip AMP 通信的底层模型是：

```text
发送端写入共享内存
          │
          ├── vring 更新队列状态
          └── Mailbox / SoftIRQ / SGI 触发核间中断
                                      │
                                      └── 接收端取出并处理消息
```

Linux 侧使用内核 RPMsg/VirtIO，RT-Thread 和 Bare-metal 侧使用 RPMsg-Lite。RPMsg 由两个单向 vring 和 vdev buffer 组成，共享内存的管理者是 Master Core。

SDK 默认 RPMsg buffer 长度为 512 Byte，扣除 16 Byte RPMsg 头部后，单条消息的最大 payload 为 496 Byte。大数据应在应用层分片，或改用共享大缓冲区 + RPMsg 控制消息的方式。

### 9.1 Linux + RT-Thread 配置

Linux Kernel 配置：

```text
CONFIG_MAILBOX=y
CONFIG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_VIRTIO=y
CONFIG_RPMSG_TTY=y                 # 可选，生成 RPMsg TTY
CONFIG_RPMSG_ROCKCHIP_TEST=y       # 可选，开启测试驱动
```

RT-Thread 在 `scons --menuconfig` 中开启：

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_LINUX_RPMSG=y
CONFIG_RT_USING_COMMON_TEST_LINUX_RPMSG_LITE=y  # 可选，测试 Demo
```
