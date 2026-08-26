# Buildroot 固件本地升级

Buildroot 支持从外部存储设备升级固件，以下是升级流程说明。关于如何编译 Buildroot 固件，请参考相应板卡维基的**编译 Buildroot 固件**页面。

## 制作升级固件

按照正常的固件编译流程，制作用于升级的固件。

升级固件不一定要全分区升级，可修改 `package-file` 文件，将不要升级的分区注释掉，或者改为 `RESERVED`，以减小升级固件的大小。

例如，修改文件 `tools/linux/Linux_Pack_Firmware/rockdev/rk356x-package-file`，将 `rootfs` 的相对路径改为 `RESERVED`，这样就不会打包根文件系统，即不升级根文件系统分区。

（1）修改文件 `tools/linux/Linux_Pack_Firmware/rockdev/package-file`。

例如，将 `rootfs` 的相对路径改为 `RESERVED`，这样就不会打包根文件系统，即不升级根文件系统分区。

```bash
# name          relative path
#
#hwdef          hwdef
package-file    package-file
bootloader      image/miniloaderall.bin
parameter       image/parameter.txt
trust           image/trust.img
uboot           image/uboot.img
misc            image/misc.img
boot            image/boot.img
recovery        image/recovery.img
rootfs          RESERVED
oem             image/oem.img
userdata:grow   image/userdata.img
backup          RESERVED
```

（2）编译固件：

```
./build.sh updateimg
```

将制作好的升级固件拷贝到 U 盘、TF 卡或者设备的 `/userdata/` 目录下，重命名为 `update.img`。

**注意：**若将升级固件放至设备的 `/userdata/` 目录，则不要打包 `userdata.img`，将 `image/userdata.img` 改为 `RESERVED`。

## 升级过程

### 方法 1

通过 qsetting QT 应用进行配置。点击 "Update" 功能选项进行操作。

### 方法 2

通过 update 命令：

```bash
# U 盘
update ota /udisk/update.img
# TF 卡
update ota /sdcard/update.img
# /userdata/
update ota /userdata/update.img
```

等待升级完成，升级成功后，设备会重新启动进入系统。
