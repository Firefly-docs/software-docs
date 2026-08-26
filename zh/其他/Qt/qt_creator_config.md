# 配置 Qt Creator

下面以 firefly-qt-5.12.2-aarch64 环境作为例子进行配置，目标平台是 Buildroot 系统：

**目标平台系统不同，配置也稍有不同，所以请仔细查看文字说明，图片仅供参考，不要照搬图片中的配置**

安装完成后，启动 Qt Creator，打开菜单 `Tools -> Options`，找到 Kits。

* 配置 Qt Versions

  点击右侧 `add` 按钮添加，选择 Qt 环境安装位置中的 qmake 即可

  qmake：`/opt/firefly-qt-5.12.2-aarch64/host/bin/qmake`

![](../../../img/Qt/Qt-config-Versions.png)

* 配置 Compilers

  点击右侧 `add` 按钮添加 gcc 和 g++ 交叉编译器的位置

  如果主机安装了 crossbuild-essential-arm64，则编译器就在 `/usr/bin/` 下

  如果使用了第三方的交叉编译器，找到安装位置并添加即可

  如果目标平台是 Buildroot，则需要使用 Buildroot Qt 环境包中的编译器

  g++：`/opt/firefly-qt-5.12.2-aarch64/host/bin/aarch64-buildroot-linux-gnu-g++`

  gcc：`/opt/firefly-qt-5.12.2-aarch64/host/bin/aarch64-buildroot-linux-gnu-gcc`

![](../../../img/Qt/Qt-config-Compilers_1.png)

![](../../../img/Qt/Qt-config-Compilers_2.png)

为方便调试，配置 Debuggers 和 Devices 用于在线调试：

* 配置 Debuggers

  首先主机中安装 gdb-multiarch：`apt install -y gdb-multiarch`

  检查目标机上是否存在 /usr/bin/gdbserver，没有的话需要安装：`apt install -y gdbserver` (Buildroot 自带，无需安装)

  回到主机的 Qt Creator，点击右侧 `add` 按钮添加 gdb

  选择主机中的 gdb-multiarch ：`/usr/bin/gdb-multiarch`

![](../../../img/Qt/Qt-config-Debuggers.png)

* 配置 Devices

  设置好设备的 IP、用户名 (root) 和密码 (firefly) 。为了方便调试，可以在设备上设置静态 IP。

  GDB server 设置为 `/usr/bin/gdbserver`

![](../../../img/Qt/Qt-config-Devices.png)

* 配置 Kits

  将前面设置的配置项添加到 Kits。

  **如果目标平台是 Ubuntu 系统，这一步也需要添加 sysroot 的路径**

![](../../../img/Qt/Qt-config-Kits.png)
