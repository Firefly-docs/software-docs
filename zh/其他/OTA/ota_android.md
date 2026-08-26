# Android OTA

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
