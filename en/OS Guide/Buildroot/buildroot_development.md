# Buildroot Development

Buildroot is a framework for building embedded Linux systems on the Linux platform. The entire Buildroot is composed of a Makefile (*.mk) script and a Kconfig (Config.in) configuration file. You can compile a complete Linux system software (including boot, kernel, rootfs, and various libraries and applications in rootfs) that can be directly flashed to the machine through the buildroot configuration and menuconfig modification, just like compiling the Linux kernel. If you want to learn more about Buildroot development, you can refer to the official Buildroot *[The Buildroot user manual](https://buildroot.org/downloads/manual/manual.html)*.

Let's take the development of **ROC-RK3568-PC**'s **Buildroot** as an example to explain.

The development topics are:

- [Directory structure](buildroot_development_directory.md)
- [Configuration](buildroot_development_configuration.md)
- [Compilation](buildroot_development_compile.md)
- [Package compilation](buildroot_development_package.md)
- [Output directory](buildroot_development_output.md)
- [Cross-compilation tool](buildroot_development_cross_compile.md)
- [Rebuild](buildroot_development_rebuild.md)
- [Add local source package](buildroot_development_local_package.md)
- [rootfs-overlay](buildroot_development_overlay.md)
- [Qt cross-compilation environment](buildroot_development_qt.md)
