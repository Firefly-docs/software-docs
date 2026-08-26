# 编译运行 Qt Creator 项目

打开 demo 程序，`Welcome -> Open Project`，选择要使用的 Kits：

![](../../../img/Qt/Qt-Choose-Kit.png)

之后打开 `Projects -> Run`，配置命令行参数，这里设置为 `-platform wayland`：

目标平台是 Ubuntu 则使用 `-platform xcb` (Ubuntu 桌面环境)，或者根据需要选择 `linuxfb`、`eglfs`

![](../../../img/Qt/Qt-command_line_arguments.png)

配置环境变量，即 `export XDG_RUNTIME_DIR=/tmp/.xdg`：

RK356X Buildroot 则需要使用 `/var/run` 而不是 `/tmp/.xdg`

目标平台是 Ubuntu 则需要根据之前设置的 `platform` 添加不同的环境变量，详情在 Qt 环境包中的说明文件中

如果目标平台的运行环境(本文开头提到的)之前已经配置好并成功运行 demo，此时可以直接点击右侧`Fetch Device Environment` 获取目标的环境变量

![](../../../img/Qt/Qt-set-environment.png)

编译运行：

点击 `Build` 交叉编译 Qt 程序；点击 `Run` 或 `Debug` 在设备上运行或调试程序。要重新运行程序时，记得手动点击 `Stop` 关闭已经运行的程序。

![](../../../img/Qt/Qt-Compile.png)

编译生成目录和 demo 目录在同一位置。
