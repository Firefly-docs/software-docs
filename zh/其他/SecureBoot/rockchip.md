## Linux 系统安全启动

说明：

1. 由于 Rockchip 平台默认的密钥签名不支持 extboot 格式的固件。所以目前在 device/rockchip 仓库的 defconfig 必须要添加 USE\_EXTBOOT=n 的选项来关闭 extboot 的编译。如果没有关闭 extboot 的编译就会出现 uboot 能正常加载，但是到了 kernel 启动阶段就会直接启动失败。
2. 不支持 rollback-index 回滚版本的设置。
3. RK\_SECURITY\_BURN\_KEY 的配置是在 spl 阶段将密钥烧入 rpmb 分区。此烧入操作是一次性的，烧入后芯片会熔断，后续无法更改。
4. 在 uboot 固件已经签名的情况下，kernel 没签名的话，则 uboot 阶段会对 kernel 进行签名检测，检测失败会自动进 loader 烧写模式。

使用 kernel 6.1 sdk 进行验证。defconfig 加入以下配置

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

uboot 配置文件 configs/firefly-linux.config 添加以下配置

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

编译前需要先生成 key 密钥

```
./build.sh security-createkeys
```

生成的 key 密钥会保存在 u-boot/keys 目录，必须妥善保管。如果密钥烧写入 optee 后，缺少密钥签名则无法启动设备。

```
lvsx@amd-epyc:~/project/rk3588_6-1 $ ls u-boot/keys/
dev.crt  dev.key  dev.pubkey  private_key.pem  public_key.pem  
```

全编译生成带签名的统一固件。这里只验证了 buildroot 系统。目前只有全编译 buildroot 系统会自动对 boot.img recovery.img 进行签名。需要预编译 kernel 和 recovery 先生成 boot.img 和 recovery.img 。

```
./build.sh
```

Ubuntu 和 Debian 系统目前没有做自动签名脚本支持。需要手动签名再打包：

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

签名成功的固件会打印 signed 。如果没有签名的固件则是 no-signed 打印。

```
Image(signed, version=0): uboot.img (FIT with uboot, trust...) is ready
Image(signed, version=0): recovery.img (FIT with kernel, fdt, resource...) is ready
Image(signed, version=0): boot.img (FIT with kernel, fdt, resource...) is ready
Image(signed): rk3588_spl_loader_v1.19.113.bin (with spl, ddr...) is ready
```
