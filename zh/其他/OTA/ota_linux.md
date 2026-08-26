# Linux OTA

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
