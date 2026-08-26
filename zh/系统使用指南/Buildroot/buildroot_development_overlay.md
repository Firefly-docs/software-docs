# Buildroot rootfs-overlay

rootfs-overlay 是一个实用功能，它能够在目标文件系统编译完成后将指定文件覆盖到某个目录。通过这种方式，可以方便地添加或修改根文件系统中的文件。

假设要在根文件系统的 `/etc/` 目录下添加文件 `overlay-test`，可以按如下步骤操作：

* 设置 rootfs-overlay 根目录

打开配置菜单 `make menuconfig`，通过设置 `BR2_ROOTFS_OVERLAY` 选项，添加用于覆盖的根目录。对于 RK3568，默认已添加目录 `board/rockchip/rk356x/fs-overlay/`。

* 添加文件到覆盖目录

```bash
cd buildroot/board/rockchip/rk356x/fs-overlay/
mkdir etc/
touch etc/overlay-test
```

* 编译

```bash
make
```

* 下载根文件系统

将编译好的根文件系统 `output/rockchip_rk3568/images/rootfs.ext2` 下载到设备。启动设备，可以看到已添加文件 `/etc/overlay-test`。

也可以通过查看 `target/` 目录，验证是否添加成功：

```bash
ls buildroot/output/rockchip_rk3568/target/etc/overlay-test
```
