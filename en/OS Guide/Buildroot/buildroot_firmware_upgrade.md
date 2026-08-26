# Buildroot Local Firmware Upgrade

Buildroot supports upgrading firmware from external storage devices. The following is the upgrade process description. For how to compile the Buildroot firmware, please refer to the **Compile Buildroot firmware** page of the corresponding board wiki.

## Make upgrade firmware

Follow the normal firmware compilation process to make the firmware for upgrade.

To upgrade firmware, it is not necessary to upgrade all partitions. You can modify the `package-file` file, comment out the partitions that do not need to be upgraded, or change to `RESERVED`, which can reduce the size of the upgraded firmware.

For example, modify the file `tools/linux/Linux_Pack_Firmware/rockdev/rk356x-package-file`, change the relative path of `rootfs` to `RESERVED`, so that the root filesystem will not be packaged, that is, the root filesystem partition will not be upgraded.

(1) Modify the file `tools/linux/Linux_Pack_Firmware/rockdev/package-file`.

For example, change the relative path of `rootfs` to `RESERVED`, so that the root filesystem will not be packaged, that is, the root filesystem partition will not be upgraded.

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

(2) Compile:

```
./build.sh updateimg
```

Copy the prepared upgrade firmware to the U disk, TF card, or the `/userdata/` directory of the device, and rename it to `update.img`.

**Note:** If the upgrade firmware is placed in the `/userdata/` directory of the device, do not package `userdata.img`, and change `image/userdata.img` to `RESERVED`.

## Upgrade

### Method 1

Configuration via the qsetting QT application. Click the "Update" function option to operate.

### Method 2

Via the update command:

```bash
# U disk
update ota /udisk/update.img
# TF card
update ota /sdcard/update.img
# /userdata/
update ota /userdata/update.img
```

Wait for the upgrade to complete. After the upgrade is successful, the device will reboot into the system.
