# Build a Yocto Image

**The process of building with the bitbake command needs to ensure that the network connection is normal. If it is a customer in inland China, you need to ensure that it can ping the external network.**

Enter the directory `<path/to/yocto/poky>` and execute the following commands in sequence:

```bash
# Install the required environment packages
# sudo apt install zstd
source oe-init-build-env

# add layer
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

Choose one of the commands to compile the complete core-image recipes. The following is an X11-based core image:

```bash
MACHINE=aio-1126bjd4 bitbake core-image-minimal
MACHINE=aio-1126bjd4 bitbake core-image-minimal-xfce
MACHINE=aio-1126bjd4 bitbake core-image-x11
MACHINE=aio-1126bjd4 bitbake core-image-sato
```

The following is a core image based on Wayland. Modify `DISPLAY_PLATFORM` to `wayland` in `/path/to/yocto/meta-rockchip/conf/machine/include/display.conf`:

```
DISPLAY_PLATFORM ?= "wayland"
# DISPLAY_PLATFORM ?= "x11"
```

After completing the modification, compile `core-image-weston`:

```bash
MACHINE=aio-1126bjd4 bitbake core-image-weston
```

If you need to change the compiled core-image recipes after already compiling one, clean the currently compiled core image before compiling the new one.

For example, to change from `core-image-minimal` to `core-image-sato`:

```bash
MACHINE=aio-1126bjd4 bitbake core-image-minimal -c clean
MACHINE=aio-1126bjd4 bitbake core-image-sato
```

If you want to compile some recipes separately, use the following commands:

```bash
# kernel
MACHINE=aio-1126bjd4 bitbake linux-rockchip

# u-boot
MACHINE=aio-1126bjd4 bitbake u-boot-rockchip

# rkmpp
MACHINE=aio-1126bjd4 bitbake rockchip-mpp

# rockchip-librga
MACHINE=aio-1126bjd4 bitbake rockchip-librga

# See more compilation objects
MACHINE=aio-1126bjd4 bitbake -s
```
