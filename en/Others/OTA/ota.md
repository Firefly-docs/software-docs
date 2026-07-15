# Rockchip OTA Upgrade

OTA, or Over-the-Air upgrade, updates a device through a network or a local package without reflashing it with RKDevTool every time.

This guide focuses on routine upgrades within the same Android or Linux system version, such as bug fixes, application updates, kernel replacement, or root file-system updates. Notes for major-version migration are provided at the end.

Implementations differ among SoCs, system versions, and SDK branches. Always follow the partition table, configuration, and upgrade tools shipped with the target product SDK.

## Solution Overview

| System | Solution | Installation | Main characteristic |
| --- | --- | --- | --- |
| Android | Recovery OTA | Reboot into Recovery and write partitions | Simple layout and lower storage use |
| Android | A/B OTA | Write the inactive slot while Android runs | Seamless installation and rollback |
| Linux | Recovery OTA | updateEngine selects Recovery installation | Supports remote or local update.img |
| Linux | A/B OTA | Write the inactive slot | Higher reliability with more storage use |

Always verify the complete target firmware with a full flash before building an OTA package.

## Android OTA

### Introduction and Flow

A normal Android OTA verifies update.zip, reboots into Recovery, and writes partitions such as boot, system, and vendor.

An A/B device has two bootable slots. update_engine writes the inactive slot while Android continues to run. After reboot, the new slot is marked successful only after it passes startup and health checks. Repeated boot failure can return the device to the old slot.

Android also supports Virtual A/B, which uses snapshots to reduce the space required by complete duplicate partitions. It adds requirements for temporary space, storage performance, and snapshot merging.

Normal OTA:

```text
Download and verify update.zip
  ↓
Reboot into Recovery
  ↓
Write target partitions
  ↓
Reboot into the new system
```

A/B OTA:

```text
Download and verify the package
  ↓
Write the inactive slot
  ↓
Select the next boot slot
  ↓
Reboot and validate
  ↓
Keep the new slot or roll back
```

A full package contains complete target partition data. A differential package is smaller, but its source firmware must exactly match the firmware installed on the device.

### A/B Configuration

Enable A/B in BoardConfig.mk:

```make
BOARD_USES_AB_IMAGE := true
```

Virtual A/B:

```make
BOARD_USES_AB_IMAGE := true
BOARD_ROCKCHIP_VIRTUAL_AB_ENABLE := true
```

U-Boot commonly requires:

```text
CONFIG_ANDROID_AB=y
```

Some platforms also require recovery.fstab_AB and slotselect on A/B partitions such as system, vendor, odm, and product.

### Build and Validate

Normal OTA:

```bash
source build/envsetup.sh
lunch PRODUCT-userdebug
make clean
make -j32
make dist -j32
./mkimage.sh ota
```

A/B OTA:

```bash
source build/envsetup.sh
lunch PRODUCT-userdebug
make clean
make -j32
make dist -j32
./mkimage_ab.sh ota
```

The first build after enabling A/B must be clean.

An A/B package can be validated with:

```bash
system/update_engine/scripts/update_device.py update.zip
```

A completed write normally reports:

```text
UPDATE_STATUS_UPDATED_NEED_REBOOT
```

After reboot, verify the Android version, active slot, hardware functions, and rollback behavior.

## Linux OTA

### Introduction and Flow

A Firefly single-system Linux OTA commonly uses updateEngine and Recovery:

1. The Normal system downloads or reads update.img.
2. updateEngine writes the upgrade flag to misc.
3. The device reboots into Recovery.
4. Recovery parses update.img and writes the selected partitions.
5. The device reboots into the Normal system.

The package is a Rockchip update.img. Its contents are selected by:

```text
tools/linux/Linux_Pack_Firmware/rockdev/package-file
```

Unneeded partitions can be marked RESERVED or removed to reduce package size.

A single-system Recovery layout must contain misc and recovery. userdata is commonly used to hold the downloaded package. If update.img is stored on userdata, do not move or overwrite userdata in the same update.

### Build the Package

Fully flash and verify the target firmware first. Then edit package-file and run the SDK packaging command, commonly:

```bash
./build.sh updateimg
```

### Install updateEngine

On Firefly Ubuntu 20.04 or 22.04:

```bash
sudo apt update
sudo apt install updateengine
```

Build from source on another distribution:

```bash
sudo apt install libdrm-dev libssl-dev libbz2-dev libcurl4-openssl-dev git
git clone https://gitlab.com/firefly-linux/external/recovery.git
cd recovery
make -j8
```

### Remote and Local Upgrade

Start an HTTP server in the directory containing update.img:

```bash
python3 -m http.server 8000
```

Run on the device:

```bash
updateEngine --misc=update --image_url=http://192.168.1.100:8000/update.img --savepath=/userdata/update.img --partition=0xFFFC00 --reboot
```

For a local package:

```bash
updateEngine --misc=update --image_url=/userdata/update.img --partition=0xFFFC00 --reboot
```

The documented example uses 0xFFFC00, while some SDKs use 0x3FFC00 by default. Partition mask definitions can change, so check the updateEngine help or source in the current SDK.

After installation, verify the system version, kernel, U-Boot, rootfs, hardware functions, preserved userdata, and Recovery logs.

## Major-Version or Partition-Layout Migration

Do not reuse a routine same-version OTA flow for a change in the Android major version, Linux distribution, partition layout, or data-encryption scheme. Before such a migration, verify at least the following:

- Compatibility among the bootloader, kernel, Recovery, and user-space components.
- Partition names, offsets, sizes, and file systems in both the source and target firmware.
- Changes to Android Verified Boot, signing keys, rollback indexes, and data encryption.
- Whether userdata can be migrated in place. Back up the data and perform a full flash if this cannot be guaranteed.

Design a dedicated migration and rollback procedure, and test it from every supported source version. If the target SDK does not explicitly support the migration through OTA, fully flash the target firmware with RKDevTool.
