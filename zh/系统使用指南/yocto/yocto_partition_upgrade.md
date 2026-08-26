# Yocto 分区固件烧写

编译生成的固件位于目录 `<path/to/yocto>/build/tmp/deploy/images/<board>/`。

```bash
$ sudo upgrade_tool di -boot boot.img
$ sudo upgrade_tool di -uboot uboot.img
$ sudo upgrade_tool di -misc misc.img
$ sudo upgrade_tool di -recovery recovery.img
```

- 分区烧写适用于调试阶段，固件验证请使用统一固件烧写。
- rootfs 不支持单独烧写，需要打包完整固件再烧写。
