## Secure Boot for Linux Systems

Notes:

1. Because the Rockchip platform's default key signing does not support extboot format firmware, the `USE_EXTBOOT=n` option must be added to the `defconfig` in the `device/rockchip` repository to disable extboot compilation. If extboot compilation is not disabled, uboot will load normally, but the kernel boot process will fail.

2. Rollback-index rollback settings are not supported.

3. The `RK_SECURITY_BURN_KEY` configuration burns the key into the rpmb partition during the spl stage. This burning operation is a one-time event; the chip will melt after burning, and it cannot be changed subsequently.

4. If the kernel is not signed when the uboot firmware is already signed, uboot will perform a signature check on the kernel. If the check fails, it will automatically enter loader burning mode.

Verification using the kernel 6.1 SDK. Add the following configuration to `defconfig`.

```
RK_SECURITY=y
RK_SECUREBOOT_METHOD="fit"
RK_SECUREBOOT_FIT=y

RK_SECURITY_OPTEE_STORAGE="rpmb"
RK_SECURITY_OPTEE_STORAGE_RPMB=y
# RK_SECURITY_OPTEE_STORAGE_SECURITY is not set
# RK_SECURITY_BURN_KEY is not set

RK_SECURITY_CHECK_METHOD="base"
RK_SECURITY_CHECK_BASE=y
# RK_SECURITY_CHECK_SYSTEM_ENCRYPTION is not set

USE_EXTBOOT=n
```

Add the following configuration to the u-boot configuration file configs/firefly-linux.config

```
# secure boot
CONFIG_BLK_DEV_DM=y
CONFIG_DM_CRYPT=y
CONFIG_BLK_DEV_CRYPTOLOOP=y
CONFIG_DM_VERITY=y
CONFIG_TEE=y
CONFIG_OPTEE=y

CONFIG_FIT_SIGNATURE=y
CONFIG_SPL_FIT_SIGNATURE=y
```

A key needs to be generated before compilation.

```
./build.sh security-createkeys
```

The generated key will be stored in the u-boot/keys directory and must be kept safe. If the key is burned into the optee, the device will not be able to boot without the key signature.

```
lvsx@amd-epyc:~/project/rk3588_6-1 $ ls u-boot/keys/
dev.crt  dev.key  dev.pubkey  private_key.pem  public_key.pem  
```

A full compilation generates a signed unified firmware. This test only verified the BuildRoot system. Currently, only a full compilation of the BuildRoot system automatically signs boot.img and recovery.img. Pre-compiling the kernel and recovery requires generating boot.img and recovery.img first.

```
./build.sh
```

Ubuntu and Debian systems currently do not support automatic signing scripts. Manual signing and repackaging are required.

```
# Manually signed firmware
cd u-boot
./make.sh CROSS_COMPILE=/home2/lvsx/project/rk3588_6-1/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu- rk3588 firefly-linux --spl-new --recovery_img ../output/recovery/ramboot.img --boot_img ../kernel/boot.img

# Repackage firmware
cp boot.img ../output/firmware/boot.img
cp recovery.img ../output/recovery/ramboot.img
cd ..
./build.sh updateimg
```

Successfully signed firmware will print "signed". Unsigned firmware will print "no-signed".

```
Image(signed, version=0): uboot.img (FIT with uboot, trust...) is ready
Image(signed, version=0): recovery.img (FIT with kernel, fdt, resource...) is ready
Image(signed, version=0): boot.img (FIT with kernel, fdt, resource...) is ready
Image(signed): rk3588_spl_loader_v1.19.113.bin (with spl, ddr...) is ready
```
