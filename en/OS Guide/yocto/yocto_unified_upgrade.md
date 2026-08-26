# Yocto Unified Firmware Upgrade

The compiled firmware is located in `<path/to/yocto>/build/tmp/deploy/images/<board>/`. The files to download are `.wic` and `update.img`. After entering loader mode, execute the following commands:

```bash
$ sudo upgrade_tool wl 0 <IMAGE NAME>.wic
$ sudo upgrade_tool uf update.img
```

- **The default login account of the firmware is: root, password: firefly. The firmware contains a common user account named firefly, and the password is firefly.**

If you are developing on a Windows PC, you can use RKdevtool to directly burn `update.img`, **without burning `<IMAGE NAME>.wic`**. Note that `update.img` is a link file, so select the actual file that the link points to.
