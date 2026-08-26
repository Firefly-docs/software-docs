# Configure Qt Creator

Here we use firefly-qt-5.12.2-aarch64 as example, the target platform uses Buildroot system:

**The configuration varies slightly depending on the target system, so please read the text carefully, pictures for reference only, do not just copy configuration in the picture**

Launch Qt Creator, open `Tools -> Options`, find Kits page.

* Select Qt Versions

  Click `add` button on the right, select qmake in the Qt environment installation location.

  qmake：`/opt/firefly-qt-5.12.2-aarch64/host/bin/qmake`

![](../../../img/Qt/Qt-config-Versions.png)

* Select Compilers

  Click `add` button on the right to add gcc and g++ compilers.

  If crossbuild-essential-arm64 is installed on host PC, then the compilers are under `/usr/bin/`.

  If using 3rd-party compilers, just find compilers' installation location.

  If the target uses Buildroot, use the compilers in the Buildroot Qt environment package.

  g++：`/opt/firefly-qt-5.12.2-aarch64/host/bin/aarch64-buildroot-linux-gnu-g++`

  gcc：`/opt/firefly-qt-5.12.2-aarch64/host/bin/aarch64-buildroot-linux-gnu-gcc`

![](../../../img/Qt/Qt-config-Compilers_1.png)

![](../../../img/Qt/Qt-config-Compilers_2.png)

For easily debug, configure gdb and devices for online debugging:  

* Select Debuggers

  First install gdb-multiarch on host：`apt install -y gdb-multiarch`

  Check if /usr/bin/gdbserver exist on target. If not, install it: `apt install -y gdbserver` (Buildroot comes with gdbserver, no need to install)

  Back to host Qt Creator, click `add` to add gdb

  Select gdb-multiarch ：`/usr/bin/gdb-multiarch`

![](../../../img/Qt/Qt-config-Debuggers.png)

* Configure Devices

  Set the IP, username (root) and password (firefly). You can use static IP on target.

  Set the GDB server: `/usr/bin/gdbserver`

![](../../../img/Qt/Qt-config-Devices.png)

* Configure Kits

  Add previous settings into Kits.

  **If target uses Ubuntu, you need to set sysroot.**

![](../../../img/Qt/Qt-config-Kits.png)
