# Buildroot rootfs-overlay

rootfs-overly is a pretty good feature, it can overwrite the specified file to a directory after the target filesystem is compiled. In this way, we can easily add or modify some files to the root filesystem.

Suppose we want to add the file `overlay-test` under the `/etc/` directory of the root filesystem, we can do as follows:

* Set rootfs-overlay root directory

Open the configuration menu `make menuconfig`, and add the root directory for coverage by setting the `BR2_ROOTFS_OVERLAY` option. For RK3568, the directory `board/rockchip/rk356x/fs-overlay/` has been added by default.

* Add files to the coverage directory

```bash
cd buildroot/board/rockchip/rk356x/fs-overlay/
mkdir etc/
touch etc/overlay-test
```

* Compile

```bash
make
```

* Download the root filesystem

Download the compiled root filesystem `output/rockchip_rk3568/images/rootfs.ext2` to the device. Start the device, you can see that the file `/etc/overlay-test` has been added.

You can also check the `target/` directory to verify whether the addition is successful:

```bash
ls buildroot/output/rockchip_rk3568/target/etc/overlay-test
```
