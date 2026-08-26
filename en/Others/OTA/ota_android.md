# Android OTA

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
