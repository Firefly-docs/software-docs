# Sign and Repackage Firmware

A key needs to be generated before compilation.

```
./build.sh security-createkeys
```

The generated key will be stored in the `u-boot/keys` directory and must be kept safe. If the key is burned into the OP-TEE, the device will not be able to boot without the key signature.

```
lvsx@amd-epyc:~/project/rk3588_6-1 $ ls u-boot/keys/
dev.crt  dev.key  dev.pubkey  private_key.pem  public_key.pem
```

A full compilation generates a signed unified firmware. This test only verified the Buildroot system. Currently, only a full compilation of the Buildroot system automatically signs `boot.img` and `recovery.img`. Pre-compiling the kernel and recovery requires generating `boot.img` and `recovery.img` first.

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

Successfully signed firmware will print `signed`. Unsigned firmware will print `no-signed`.

```
Image(signed, version=0): uboot.img (FIT with uboot, trust...) is ready
Image(signed, version=0): recovery.img (FIT with kernel, fdt, resource...) is ready
Image(signed, version=0): boot.img (FIT with kernel, fdt, resource...) is ready
Image(signed): rk3588_spl_loader_v1.19.113.bin (with spl, ddr...) is ready
```
