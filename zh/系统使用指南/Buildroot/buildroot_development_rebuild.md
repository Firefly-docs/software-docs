# Buildroot 重建

对于重建的具体说明，可以查看文档 `buildroot/docs/manual/rebuilding-packages.txt`。

## 重建软件包

在开发过程中，若修改了某个软件包的源码，Buildroot 不会重新编译该软件包。可以按如下方式操作：

* 方式一

```bash
make <package>-rebuild
```

* 方式二

```bash
# 删除软件包的编译输出目录
rm -rf output/rockchip_rk3568/build/<package>-<version>
# 编译
make <package>
```

## 完全重建

当通过 `make menuconfig`、`make xconfig` 或其他配置工具更改系统配置时，Buildroot 不会尝试检测应重建系统的哪些部分。在某些情况下，Buildroot 应该重建整个系统，在某些情况下，仅应重建软件包的特定子集。但是以完全可靠的方式检测到这一点非常困难，因此 Buildroot 开发人员已决定不尝试这样做。

### 何时需要完全重建

* 更改目标体系结构配置时，需要完全重建；
* 更改工具链配置时，需要完全重建；
* 将其他软件包添加到配置中时，不一定需要完全重建；
* 从配置中删除软件包时，Buildroot 不会执行任何特殊操作。它不会从目标根文件系统或工具链中删除此软件包安装的文件，需要完全重建才能删除这些文件；
* 更改软件包的子选项时，不会自动重建软件包；
* 对根文件系统框架进行更改时，需要完全重建；

一般而言，当你遇到构建错误并且不确定所做的配置更改可能带来的后果时，请进行完全重建。具体说明可以查看文档 `rebuilding-packages.txt`。

### 如何完全重建

* 方式一

直接删除编译输出目录，之后重新进行配置、编译。

```bash
rm -rf output/
```

* 方式二

执行如下命令，会删除编译输出并重新编译。

```bash
make clean all
```
