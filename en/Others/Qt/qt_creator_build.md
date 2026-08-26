# Compile and Run with Qt Creator

Click `Welcome -> Open Project` to open a demo project, choose the Kits:

![](../../../img/Qt/Qt-Choose-Kit.png)

Then click `Projects -> Run` to set platform parameters `-platform wayland`：

Ubuntu target need to use `-platform xcb`, or `linuxfb`, `eglfs` as you need.

![](../../../img/Qt/Qt-command_line_arguments.png)

Configure envionment variables `export XDG_RUNTIME_DIR=/tmp/.xdg`:

RK356X Buildroot needs to use `/var/run`, not `/tmp/.xdg`

Ubuntu target needs to set different variables according to platform selected above, read the README file in Qt environment package.

If the target Qt environment (mentioned at the beginning) is already prepared and demo ran successfully, then you can click `Fetch Device Environment` in this step.

![](../../../img/Qt/Qt-set-environment.png)

Compile and run:

Click `Build` to compile Qt program; `Run` or `Debug` for running and debugging.

To restart the program, remember to click `Stop` first.

![](../../../img/Qt/Qt-Compile.png)

The output directory has the same location as demo directory.
