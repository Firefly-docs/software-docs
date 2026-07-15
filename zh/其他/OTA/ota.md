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

## Android OTA

### 介绍

Rockchip Android 平台主要使用普通 OTA 和 A/B OTA。

普通 OTA 下载并校验 update.zip 后，设备重启进入 Recovery。Recovery 把升级包中的 boot、system、vendor 等内容写入目标分区，完成后重新启动 Android。

A/B 系统具有 A、B 两套可启动分区。设备从当前 Slot 运行时，update_engine 把新系统写入另一 Slot。重启进入新系统后，如果启动和健康检查成功，就把新 Slot 标记为可正常启动；如果连续启动失败，则回退到旧 Slot。

Android 还支持 Virtual A/B。Virtual A/B 使用快照完成升级，可以减少完整双分区的空间占用，但对临时空间、存储性能和快照合并过程有额外要求。

### 原理

普通 OTA：

```text
下载 update.zip
  ↓
校验文件和签名
  ↓
重启进入 Recovery
  ↓
写入目标分区
  ↓
重启进入新系统
```

A/B OTA：

```text
下载升级包
  ↓
校验文件和签名
  ↓
update_engine 写入非当前 Slot
  ↓
设置下次启动 Slot
  ↓
重启并验证新系统
  ↓
成功则保留新系统，失败则回退旧系统
```

同版本 OTA 一般分为完整包和差分包：

- 完整包包含目标版本需要的完整分区数据，包体较大，但对源固件的限制较少。
- 差分包只保存源固件和目标固件之间的差异，包体较小，但设备当前固件必须与制作差分包时使用的源固件完全一致。

### 配置 A/B

普通 OTA 不需要开启 A/B。A/B 项目通常在 BoardConfig.mk 中配置：

```make
BOARD_USES_AB_IMAGE := true
```

Virtual A/B：

```make
BOARD_USES_AB_IMAGE := true
BOARD_ROCKCHIP_VIRTUAL_AB_ENABLE := true
```

U-Boot 通常需要：

```text
CONFIG_ANDROID_AB=y
```

部分平台还需要配置 recovery.fstab_AB，并为 system、vendor、odm、product 等分区增加 slotselect。应优先使用当前 SDK 提供的配置模板。

### 编译 OTA 包

普通 OTA：

```bash
source build/envsetup.sh
lunch PRODUCT-userdebug
make clean
make -j32
make dist -j32
./mkimage.sh ota
```

A/B OTA：

```bash
source build/envsetup.sh
lunch PRODUCT-userdebug
make clean
make -j32
make dist -j32
./mkimage_ab.sh ota
```

第一次开启 A/B 后，应执行 clean 再重新编译。

### 验证 A/B 升级

开发阶段可以使用 Android update_engine 的测试脚本：

```bash
system/update_engine/scripts/update_device.py update.zip
```

升级写入完成后通常会显示：

```text
UPDATE_STATUS_UPDATED_NEED_REBOOT
```

重启后应检查 Android 版本、当前 Slot、主要硬件功能和自动回滚。

## Linux OTA

### 介绍

Firefly Linux 的单系统 OTA 通常使用 updateEngine 和 Recovery：

- Normal 系统负责下载或读取 update.img。
- updateEngine 在 misc 分区写入升级标志。
- 设备重启进入 Recovery。
- Recovery 解析 update.img，并写入指定分区。
- 升级完成后，设备自动重启进入正常系统。

单系统 Recovery OTA 结构简单、占用空间较少，但升级期间设备不能正常运行。对自动回滚要求较高的产品可以评估 Linux A/B。

### 原理

Linux OTA 使用 Rockchip 格式的 update.img。具体包含哪些分区由 package-file 决定：

```text
tools/linux/Linux_Pack_Firmware/rockdev/package-file
```

不需要升级的分区可以设置为 RESERVED 或从打包列表中移除。例如只更新 Kernel、U-Boot 和 Bootloader 时，可以不打包 rootfs，从而减小 update.img。

升级流程：

```text
获取 update.img
  ↓
校验升级包
  ↓
updateEngine 把升级标志写入 misc
  ↓
设备重启进入 Recovery
  ↓
Recovery 写入指定分区
  ↓
设备重启进入正常系统
```

单系统 Recovery OTA 的分区表中至少需要包含 misc 和 recovery。userdata 常用于保存下载的 update.img。

如果升级包保存在 userdata 中，不应在升级过程中修改 userdata 的起始位置，也不要把 userdata.img 放入同一个 OTA 包。

### 制作 OTA 固件

先完整编译和烧写目标固件，确认目标系统能够正常启动。

根据需要修改 package-file，然后使用当前 SDK 的打包命令生成 update.img。常见命令为：

```bash
./build.sh updateimg
```

生成后应检查 update.img 中包含的分区，避免意外更新 userdata、misc、parameter 等分区。

### 安装 updateEngine

Firefly Ubuntu 20.04 和 Ubuntu 22.04 软件源可以直接安装：

```bash
sudo apt update
sudo apt install updateengine
```

其他 Linux 发行版可以从源码编译：

```bash
sudo apt install libdrm-dev libssl-dev libbz2-dev libcurl4-openssl-dev git
git clone https://gitlab.com/firefly-linux/external/recovery.git
cd recovery
make -j8
```

### 远程升级

在存放 update.img 的主机目录启动 HTTP 服务：

```bash
python3 -m http.server 8000
```

设备端执行：

```bash
updateEngine --misc=update --image_url=http://192.168.1.100:8000/update.img --savepath=/userdata/update.img --partition=0xFFFC00 --reboot
```

### 本地升级

把 update.img 放到设备的 /userdata 目录：

```bash
updateEngine --misc=update --image_url=/userdata/update.img --partition=0xFFFC00 --reboot
```

也可以使用 U 盘或 SD 卡，但 Recovery 必须能够自动识别并挂载对应介质。

参数说明：

- --misc=update：在 misc 中写入升级标志。
- --image_url：指定远程或本地升级包路径。
- --savepath：指定远程升级包下载后的保存路径。
- --partition：指定需要升级的分区。
- --reboot：准备完成后自动重启进入 Recovery。

示例使用 0xFFFC00，部分 SDK 的默认值为 0x3FFC00。分区掩码定义可能随 SDK 变化，使用前必须查看当前 updateEngine 的帮助或源码。

### 验证升级

升级完成后应检查：

- 系统版本是否正确。
- Kernel、U-Boot、rootfs 和应用版本是否符合预期。
- 网络、显示、存储、音频等主要功能是否正常。
- userdata 中的用户数据是否保留。
- Recovery 日志中是否存在分区写入失败。

## 跨版本或分区布局升级

跨 Android 主版本、Linux 发行版、分区布局或数据加密方案升级，不应直接沿用同版本 OTA 流程。升级前至少应确认：

- Bootloader、Kernel、Recovery 和用户空间组件之间是否兼容。
- 新旧固件的分区名称、起始位置、大小和文件系统是否一致。
- Android Verified Boot、签名密钥、防回滚版本和数据加密方式是否发生变化。
- userdata 是否能够原地迁移；无法保证时，应先备份数据并采用完整烧写。

此类升级应单独设计迁移与回退方案，并在所有支持的源版本上逐一验证。若当前 SDK 没有明确提供跨版本 OTA 支持，建议使用 RKDevTool 完整烧写目标固件。
