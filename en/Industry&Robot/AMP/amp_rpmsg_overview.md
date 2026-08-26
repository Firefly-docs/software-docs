# AMP RPMsg Overview

## 9. RPMsg inter-core communication

The underlying Rockchip AMP communication model is:

```text
Sender writes shared memory
          │
          ├── Updates vring queue state
          └── Triggers an inter-core interrupt through Mailbox / SoftIRQ / SGI
                                      │
                                      └── Receiver obtains and processes the message
```

Linux uses the in-kernel RPMsg/VirtIO framework, while RT-Thread and bare-metal systems use RPMsg-Lite. RPMsg consists of two unidirectional vrings and a vdev buffer. The Master Core manages the shared memory.

The default RPMsg buffer size in the SDK is 512 bytes. After subtracting the 16-byte RPMsg header, the maximum payload of one message is 496 bytes. Large data should be fragmented at the application layer, or transferred through a dedicated shared buffer with RPMsg used only for control messages.

### 9.1 Linux + RT-Thread configuration

Linux Kernel configuration:

```text
CONFIG_MAILBOX=y
CONFIG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_ROCKCHIP_MBOX=y
CONFIG_RPMSG_VIRTIO=y
CONFIG_RPMSG_TTY=y                 # Optional: create an RPMsg TTY
CONFIG_RPMSG_ROCKCHIP_TEST=y       # Optional: enable the test driver
```

Enable the following RT-Thread options through `scons --menuconfig`:

```text
CONFIG_RT_USING_RPMSG_LITE=y
CONFIG_RT_USING_LINUX_RPMSG=y
CONFIG_RT_USING_COMMON_TEST_LINUX_RPMSG_LITE=y  # Optional test demo
```
