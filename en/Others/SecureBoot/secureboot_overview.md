# Secure Boot for Linux Systems

Notes:

1. Because the Rockchip platform's default key signing does not support extboot format firmware, the `USE_EXTBOOT=n` option must be added to the `defconfig` in the `device/rockchip` repository to disable extboot compilation. If extboot compilation is not disabled, uboot will load normally, but the kernel boot process will fail.

2. Rollback-index rollback settings are not supported.

3. The configuration of RK\_SECURITY\_BURN\_KEY involves burning the key during the spl phase. This burning operation is a one-time event; the chip will melt after burning, and it cannot be changed subsequently.

4. If the kernel is not signed when the uboot firmware is already signed, uboot will perform a signature check on the kernel. If the check fails, it will automatically enter loader burning mode.
