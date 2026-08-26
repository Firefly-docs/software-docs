# Buildroot 配置

选择默认配置文件：

```bash
# 进入 Firefly_Linux_SDK 根目录
cd path/to/Firefly_Linux_SDK/
# 选择配置文件
# `configs/rockchip_rk3568_defconfig`
source envsetup.sh rockchip_rk3568
```

执行完成后会生成编译输出目录 `output/rockchip_rk3568`，后续可以在该目录下执行 `make` 相关操作。

## 软件包配置

打开配置界面：

```bash
make menuconfig
```

![](../../../img/buildroot/Buildroot_menuconfig.png)

我们可以在配置界面添加或裁剪一些工具，按需求定制系统功能。以添加 `qt53d` 为例：

输入 `/` 进入搜索界面，输入要查找的内容 `qt53d`，按回车进行搜索：

![](../../../img/buildroot/Buildroot_serach_qt53d.png)

![](../../../img/buildroot/Buildroot_qt53d.png)

选择 `1` 跳转到对应页面，按空格选中配置：

![](../../../img/buildroot/Buildroot_select_qt53d.png)

配置完成后，移动到 `Save` 按回车保存到 `.config`；移动到 `Exit` 按回车退出。

![](../../../img/buildroot/Buildroot_Save-Exit.png)

保存配置文件：

```bash
make savedefconfig
```

将修改保存到配置文件 `configs/rockchip_rk3568_defconfig`。

## Busybox 配置

打开配置界面，进行配置：

```bash
make busybox-menuconfig
```

![](../../../img/buildroot/Buildroot_busybox-menuconfig.png)

配置完成后，移动到 `Exit` 按回车退出，在弹窗页面选择 `Yes` 保存到 `.config`。

![](../../../img/buildroot/Buildroot_busybox-save.png)

保存配置文件：

```bash
make busybox-update-config
```

将修改保存到配置文件 `board/rockchip/common/base/busybox.config`。
