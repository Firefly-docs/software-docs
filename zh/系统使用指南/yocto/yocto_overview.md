# Yocto Project 相关概述

Yocto Project 是一个专注于嵌入式 Linux 操作系统开发的开源协作项目，它提供灵活的工具集和开发环境，允许全球的嵌入式设备开发人员通过共享技术、软件堆栈、配置和用于创建定制 Linux 映像的最佳实践进行协作。有关 Yocto 项目的更多信息，请参阅 Yocto Project 官网：[www.yoctoproject.org/](https://www.yoctoproject.org/)。官网提供 [Yocto Project Reference Manual](https://docs.yoctoproject.org/current/ref-manual/index.html)、[Yocto Project Overview](https://docs.yoctoproject.org/) 等相关文档，详细描述如何构建系统。

## Yocto Project Release layer 介绍

| layer  | 路径 | 优先级（数字越大优先级越高） | 描述 |
| :--------: | :-------: | :-------: | :-------: |
| meta-oe | meta-openembedded/meta-oe | 6 | 包含大量附加 recipes |
| meta-python | meta-openembedded/meta-python | 7 | 提供 Python recipes |
| meta-qt5 | meta-qt5 | 7 | 提供 Qt5 recipes |
| meta-clang | meta-clang | 7 | 提供 clang 编译器 |
| meta-rockchip | meta-rockchip | 9 | 提供 Rockchip 板级支持 |
| meta | meta | 5 | 包含 OpenEmbedded-Core 元数据 |
| meta-poky | meta-poky | 5 | 保存 Poky 参考发行版配置 |
| meta-yocto-bsp | meta-yocto-bsp | 5 | Yocto Project 参考硬件板级支持包配置 |
| meta-chromium | meta-chromium | 7 | 提供 Chromium 浏览器 recipe |
