# Yocto 统一固件烧写

编译生成的固件位于目录 `<path/to/yocto>/build/tmp/deploy/images/<board>/`，待下载的文件为 `.wic` 与 `update.img`。进入 loader 模式后执行如下命令：

```bash
$ sudo upgrade_tool wl 0 <IMAGE NAME>.wic
$ sudo upgrade_tool uf update.img
```

- **固件默认登录账号为：root，密码为：firefly。固件含有普通用户账号名称为：firefly，密码为：firefly。**

如果在 Windows PC 上开发，使用 RKdevtool 直接烧录 `update.img` 即可，**不需要烧录 `<IMAGE NAME>.wic`**。需要注意的是，`update.img` 是一个链接文件，实际需要选择链接文件所指向的文件。
