# Buildroot 目录结构

Buildroot SDK 位于 Firefly_Linux_SDK 目录，其目录结构如下：

```bash
buildroot/
├── arch                # CPU 架构的构建、配置文件
├── board               # 具体单板相关的文件
├── boot                # Bootloaders 的构建、配置文件
├── build
├── CHANGES             # Buildroot 修改日志
├── Config.in
├── Config.in.legacy
├── configs             # 具体单板的 Buildroot 配置文件
├── COPYING
├── DEVELOPERS
├── dl                  # 下载的程序、源码压缩包、补丁等
├── docs                # 文档
├── fs                  # 各种文件系统的构建、配置文件
├── linux               # Linux 的构建、配置文件
├── Makefile
├── Makefile.legacy
├── output              # 编译输出目录
├── package             # 所有软件包的构建、配置文件
├── README              # Buildroot 简单说明
├── support             # 为 Bulidroot 提供功能支持的脚本、配置文件
├── system              # 制作根文件系统的构建、配置文件
├── toolchain           # 交叉编译工具链的构建、配置文件
└── utils               # 实用工具
```
