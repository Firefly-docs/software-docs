# Yocto Project Overview

The Yocto Project is an open source collaborative project focused on embedded Linux operating system development. It provides a flexible toolset and development environment that allows embedded device developers worldwide to share technologies, software stacks, configurations, and tools for creating custom Linux images. For more information, refer to the official Yocto Project website: [www.yoctoproject.org/](https://www.yoctoproject.org/). The website provides the [Yocto Project Reference Manual](https://docs.yoctoproject.org/current/ref-manual/index.html), the [Yocto Project Overview](https://docs.yoctoproject.org/), and other related documents that describe how to build the system.

## Yocto Project release layers

| Layer  | Path | Priority (higher numbers have higher priority) | Description |
| :--------: | :-------: | :-------: | :-------: |
| meta-oe | meta-openembedded/meta-oe | 6 | Contains a large amount of additional recipes |
| meta-python | meta-openembedded/meta-python | 7 | Provides Python recipes |
| meta-qt5 | meta-qt5 | 7 | Provides Qt5 recipes |
| meta-clang | meta-clang | 7 | Provides the clang compiler |
| meta-rockchip | meta-rockchip | 9 | Provides Rockchip board-level support |
| meta | meta | 5 | Contains the OpenEmbedded-Core metadata |
| meta-poky | meta-poky | 5 | Holds the configuration for the Poky reference distribution |
| meta-yocto-bsp | meta-yocto-bsp | 5 | Configuration for the Yocto Project reference hardware board support package |
| meta-chromium | meta-chromium | 7 | Provides the Chromium browser recipe |
