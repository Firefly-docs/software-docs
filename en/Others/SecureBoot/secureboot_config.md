# Configure Secure Boot

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
