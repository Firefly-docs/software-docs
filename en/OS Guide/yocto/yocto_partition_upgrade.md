# Yocto Partition Firmware Upgrade

The compiled firmware is located in `<path/to/yocto>/build/tmp/deploy/images/<board>/`.

```bash
$ sudo upgrade_tool di -boot boot.img
$ sudo upgrade_tool di -uboot uboot.img
$ sudo upgrade_tool di -misc misc.img
$ sudo upgrade_tool di -recovery recovery.img
```

- Partition burning is suitable for the debugging stage. For firmware verification, use the unified firmware burning procedure.
- Rootfs does not support separate burning. Pack the complete firmware before burning.
