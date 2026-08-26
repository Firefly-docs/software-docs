# 固件签名和重新打包

编译前需要先生成 key 密钥。

```
./build.sh security-createkeys
```

生成的 key 密钥会保存在 `u-boot/keys` 目录，必须妥善保管。如果密钥烧写入 OP-TEE 后，缺少密钥签名则无法启动设备。

```
lvsx@amd-epyc:~/project/rk3588_6-1 $ ls u-boot/keys/
dev.crt  dev.key  dev.pubkey  private_key.pem  public_key.pem
```

全编译生成带签名的统一固件。这里只验证了 Buildroot 系统。目前只有全编译 Buildroot 系统会自动对 `boot.img`、`recovery.img` 进行签名。需要预编译 kernel 和 recovery 时，先生成 `boot.img` 和 `recovery.img`。

```
./build.sh
```

Ubuntu 和 Debian 系统目前没有自动签名脚本支持，需要手动签名再打包：

```
# 手动签名固件
cd u-boot
./make.sh CROSS_COMPILE=/home2/lvsx/project/rk3588_6-1/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu- rk3588 firefly-linux --spl-new --recovery_img ../output/recovery/ramboot.img --boot_img ../kernel/boot.img

# 重新打包固件
cp boot.img ../output/firmware/boot.img
cp recovery.img ../output/recovery/ramboot.img
cd ..
./build.sh updateimg
```

签名成功的固件会打印 `signed`，未签名的固件会打印 `no-signed`。

```
Image(signed, version=0): uboot.img (FIT with uboot, trust...) is ready
Image(signed, version=0): recovery.img (FIT with kernel, fdt, resource...) is ready
Image(signed, version=0): boot.img (FIT with kernel, fdt, resource...) is ready
Image(signed): rk3588_spl_loader_v1.19.113.bin (with spl, ddr...) is ready
```
