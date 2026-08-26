# 编译 Yocto 映像文件

**使用 bitbake 命令构建的过程需要保证网络连接正常，如果是中国内陆客户需要保证能 ping 通外网。**

进入目录 `<path/to/yocto/poky>`，按顺序执行如下命令：

```bash
# Install the required environment packages
# sudo apt install zstd
source oe-init-build-env

# 添加 layer（只需要执行一次）
bitbake-layers add-layer ../../meta-openembedded/meta-oe
bitbake-layers add-layer ../../meta-openembedded/meta-python
bitbake-layers add-layer ../../meta-openembedded/meta-networking
bitbake-layers add-layer ../../meta-openembedded/meta-multimedia
bitbake-layers add-layer ../../meta-openembedded/meta-gnome
bitbake-layers add-layer ../../meta-openembedded/meta-xfce
bitbake-layers add-layer ../../meta-lts-mixins
bitbake-layers add-layer ../../meta-clang
bitbake-layers add-layer ../../meta-browser/meta-chromium
bitbake-layers add-layer ../../meta-rockchip
```

选择其中之一命令来编译完整 core-image recipes。以下是基于 X11 的 core-image：

```bash
MACHINE=aio-1126bjd4 bitbake core-image-minimal
MACHINE=aio-1126bjd4 bitbake core-image-minimal-xfce
MACHINE=aio-1126bjd4 bitbake core-image-x11
MACHINE=aio-1126bjd4 bitbake core-image-sato
```

以下是基于 Wayland 的 core-image。需要在 `/path/to/yocto/meta-rockchip/conf/machine/include/display.conf` 中将 DISPLAY_PLATFORM 修改为 wayland：

```
DISPLAY_PLATFORM ?= "wayland"
# DISPLAY_PLATFORM ?= "x11"
```

完成上述修改后，执行命令编译 core-image-weston：

```bash
MACHINE=aio-1126bjd4 bitbake core-image-weston
```

注意：如果已经完整编译过一次 core-image，之后需要更换编译的 core-image recipes，则需要先清理当前编译过的 core-image，再开始编译新的 core-image。

例如，当前编译的是 `core-image-minimal`，需要更换成 `core-image-sato`：

```bash
MACHINE=aio-1126bjd4 bitbake core-image-minimal -c clean
MACHINE=aio-1126bjd4 bitbake core-image-sato
```

如果想单独编译部分 recipes，可以参考以下内容：

```bash
# kernel
MACHINE=aio-1126bjd4 bitbake linux-rockchip

# u-boot
MACHINE=aio-1126bjd4 bitbake u-boot-rockchip

# rkmpp
MACHINE=aio-1126bjd4 bitbake rockchip-mpp

# rockchip-librga
MACHINE=aio-1126bjd4 bitbake rockchip-librga

# 参看更多编译对象
MACHINE=aio-1126bjd4 bitbake -s
```
