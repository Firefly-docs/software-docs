# Rockchip OTA 升级简介

OTA（Over-the-Air）用于通过网络或本地升级包更新设备系统，无需每次使用数据线和 RKDevTool 重新烧写。

本文主要介绍同一 Android 或 Linux 系统版本内的日常 OTA 升级，例如修复问题、更新应用、替换 Kernel 或更新 rootfs。跨系统版本升级的注意事项放在文末。

不同芯片、系统版本和 SDK 的实现可能存在差异，实际操作时应以当前产品 SDK 的分区表、配置和升级程序为准。

## OTA 方案概览

| 系统 | 方案 | 升级方式 | 主要特点 |
| --- | --- | --- | --- |
| Android | 普通 OTA | 重启进入 Recovery 后写入分区 | 分区结构简单，占用空间较少 |
| Android | A/B OTA | 当前系统运行时写入另一 Slot | 支持无缝升级和失败回退 |
| Linux | Recovery OTA | updateEngine 设置升级标志，重启进入 Recovery | 可通过网络或本地 update.img 升级 |
| Linux | A/B OTA | 当前 Slot 运行时写入另一 Slot | 可靠性更高，但需要更多存储空间 |

无论使用哪种方案，都应先通过完整烧写验证目标固件，再制作和测试 OTA 包。
