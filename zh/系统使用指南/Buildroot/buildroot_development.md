# Buildroot 开发

Buildroot 是 Linux 平台上一个构建嵌入式 Linux 系统的框架。整个 Buildroot 是由 Makefile（*.mk）脚本和 Kconfig（Config.in）配置文件构成的。你可以和编译 Linux 内核一样，通过 Buildroot 配置、menuconfig 修改，编译出一个完整的可以直接烧写到机器上运行的 Linux 系统软件（包含 boot、kernel、rootfs 以及 rootfs 中的各种库和应用程序）。若要了解更多 Buildroot 开发相关内容，可以参考 Buildroot 官方的 [《开发手册》](https://buildroot.org/downloads/manual/manual.html)。

下面以 **RK356x** 平台的 **Buildroot** 开发为例进行说明。

开发主题如下：

- [目录结构](buildroot_development_directory.md)
- [配置](buildroot_development_configuration.md)
- [编译](buildroot_development_compile.md)
- [软件包编译](buildroot_development_package.md)
- [编译输出目录](buildroot_development_output.md)
- [交叉编译工具](buildroot_development_cross_compile.md)
- [重建](buildroot_development_rebuild.md)
- [新增本地源码包](buildroot_development_local_package.md)
- [rootfs-overlay](buildroot_development_overlay.md)
- [Qt 交叉编译环境支持](buildroot_development_qt.md)
