# Buildroot Usage

## Desktop application

The officially released Buildroot firmware supports the Wayland desktop environment and some Qt applications by default, as shown below:

![](../../../img/buildroot/Buildroot_Desktop.png)

### multivideoplayer

The multivideoplayer is used to test the device's multi-channel video playback ability, display ability and hardware decoding ability. 

![](../../../img/buildroot/buildroot_multi_video.png)

### qfm

The qfm is a file browser.

![](../../../img/buildroot/buildroot_file_manage.png)

### qplayer

The qplayer is a multi-function player, it can play videos, audios and display pictures. 

![](../../../img/buildroot/buildroot_qplayer.png)

### qcamera

The qcamera is a camera application, can take photos and record videos.

![](../../../img/buildroot/buildroot_qcamera.png)

Launch the qcamera when device is connected to a camera, qcamera will automatically display the camera pictures. And the buttons on the right side are:

* Image Mode: Now is image mode, click to change to the video record mode.
* Capture: capture image，it will change to Record button when in video record mode.
* Exit: close the application.

### qsetting

The qsetting is a system setting tool, you can config WiFi connection, bluetooth connection and perform factory reset, firmware update in it.

![](../../../img/buildroot/buildroot_setting.png)


## User and password

* User: root
* Password: firefly


## Ethernet configuration

Buildroot's network configuration needs to use the `/etc/network/interfaces` configuration file. After the configuration is complete, run `/etc/init.d/S40network restart` to restart the network. Manual debugging can directly use `ifdown -a` and `ifup -a` to restart the network.

### Common configuration

Configuration file example: The following configuration file sets the eth0 network card to a dynamic IP address and eth1 to a static IP address

Note: The file format of `/etc/network/interfaces` is strict. If you encounter `Error: either "local" is duplicate, or "/24" is a garbage.`, it is very likely that there is one more space in the configuration file.

````
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
address 168.168.110.137
netmask 255.255.0.0
broadcast 168.168.1.255
gateway 168.168.0.1
````

(1) inet static: Define a static IP address. The supported options are:

````
address address
        Address (dotted quad/netmask) required

netmask mask
        Netmask (dotted quad or number of bits) deprecated

broadcast broadcast_address
        Broadcast address (dotted quad, + or -) deprecated. Default value: "+"

metric metric
        Routing metric for default gateway (integer)

gateway address
        Default gateway (dotted quad)

pointopoint address
        Address of other end point (dotted quad). Note the spelling of "point-to".

hwaddress address
        Link local address or "random".

mtu size
        MTU size

scope Address validity scope. Possible values: global, link, host
````

(2) inet dhcp: Obtain an IP address through the DHCP protocol. The supported options are:

````
hostname hostname
        Hostname to be requested (pump, dhcpcd, udhcpc)

metric metric
        Metric for added routes (dhclient)

leasehours leasehours
        Preferred lease time in hours (pump)

leasetime leasetime
        Preferred lease time in seconds (dhcpcd)

vendor vendor
        Vendor class identifier (dhcpcd)

client client
        Client identifier (dhcpcd)

hwaddress address
        Hardware address.
````

(3) inet manual: No IP address is defined for the interface. Typically used by an interface that is a member of a bridge or aggregation, an interface that needs to operate in promiscuous mode (*for example, port mirroring or network TAP*), or an interface that has a VLAN device configured on it. This is one way to keep the interface without an IP address. The supported options are:

````
hwaddress address
        Link local address or "random".

mtu size
        MTU size
````

### advanced settings

`/etc/network/interfaces` supports setting to run Linux command line commands when the network card is shut down/started. Since `/etc/network/interfaces` supports relatively limited functionality, this can be very helpful when configuring network configurations such as static routes, default routes, etc.

Supported optional options are: pre-up, up, post-up, pre-down, down, post-down, after these options, add the command line.

````
pre-up    : Action before network card is up
up        : Action when network card is up
post-up	  : Action after network card is up
pre-down  : Action before network card is down
down      : Action when network card is down
post-down : Action after network card is down
````

Configuration example: configure a static route for the eth1 network card

````
auto eth1
iface eth1 inet static
address 192.168.3.1
netmask 255.255.255.0
broadcast 192.168.3.255
post-up ip route add 192.168.4.0/24 via 192.168.3.2 dev $IFACE
````

Configuration example: create a bridge, bind eth0 and eth1 to the bridge, and use it as a LAN port

````
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet manual
pre-up ifconfig $IFACE up
post-down ifconfig $IFACE down

auto eth1
iface eth1 inet manual
pre-up ifconfig $IFACE up
post-down ifconfig $IFACE down

auto br0
iface br0 inet static
address 192.168.2.1
netmask 255.255.255.0
broadcast 192.168.2.255
pre-up brctl addbr $IFACE
pre-up brctl addif $IFACE eth0
pre-up brctl addif $IFACE eth1
bridge_ports eth0 eth1
post-down brctl delif $IFACE eth0
post-down brctl delif $IFACE eth1
post-down ifconfig $IFACE down
post-down brctl delbr $IFACE
````


## WiFi connection

### Modify the configuration file

#### Method 1

Configuration via the qsetting QT application.

#### Method 2

Modify the following files:

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
````

Add the following configuration items

```bash
network={
ssid="WiFi-AP" // WiFi name
psk="12345678" // WiFi password
key_mgmt=WPA-PSK // encryption method
# key_mgmt=NONE // no encryption
}
````

Start the wpa_supplicant process

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
````

### Temporary modification method

Modify the following files:

```bash
vi /data/cfg/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
````

Start the wpa_supplicant process:

```bash
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
````

#### Configure WiFi via wpa_cli

Common commands:

```bash
wpa_cli -i wlan0 scan // Search for nearby wifi networks
wpa_cli -i wlan0 scan_result // print search wifi network
wpa_cli -i wlan0 add_network // add a network connection
````

If the encryption method to connect is [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][ESS] (wpa encryption), the wifi name is name, and the wifi password is: psk. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 psk '"psk"'
wpa_cli -i wlan0 set_network 0 key_mgmt WPA-PSK
wpa_cli -i wlan0 enable_network 0 //Enable WiFi
````

If the connection encryption method is [WEP][ESS] (wep encryption), the wifi name is name, and the wifi password is psk. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 set_network 0 wep_key0 '"psk"'
wpa_cli -i wlan0 enable_network 0
````

If the connection encryption method is [ESS] (no encryption), the wifi name is name. The operation is as follows:

```bash
wpa_cli -i wlan0 set_network 0 ssid '"name"'
wpa_cli -i wlan0 set_network 0 key_mgmt NONE
wpa_cli -i wlan0 enable_network 0
````

Enable to save WIFI connection information

```bash
wpa_cli -i wlan0 set update_config 1
````

Save WIFI connection information

```bash
wpa_cli -i wlan0 save_config
````

connect an existing connection

```bash
wpa_cli -i wlan0 list_network // List all saved connections
wpa_cli -i wlan0 select_network 0 // connect to the first saved connection
wpa_cli -i wlan0 enable_network 0 // enable the first saved connection
````

Turn off WiFi

```bash
ifconfig wlan0 down
````


## Audio/Video playback

```bash
# Play wav
aplay test.wav
gstwavplay.sh test.wav

# Play mp3
mp3play.sh test.mp3
gstmp3play.sh test.mp3

# Play mp4
gstmp4play.sh test.mp4
gstvideoplay.sh test.mp4
```


## SSH

The officially released SDK has ssh enabled by default, the user is "root", and the password is "firefly". If you do not need to modify the user login password, you can skip this chapter.

### Modification method

- Enable SSH related options

   - openssh

   ````
   BR2_PACKAGE_OPENSSH=y
   ````

   - Configure the login account root and password

   ````
   BR2_TARGET_ENABLE_ROOT_LOGIN=y
   BR2_TARGET_GENERIC_ROOT_PASSWD="firefly"
   ````

- Modify configuration files

   - Modify the `/etc/ssh/sshd_config` file in the board

   ````
   PermitRootLogin yes
   ````


## External storage device

Buildroot supports automatic mounting of external storage devices:

- U disk mount path: `/udisk`
- TF card mounting path: `/sdcard`


## Factory Reset

**Note: This factory setting means returning to the original state of the device after the last firmware upgrade. **

### method 1

Configure through the qsetting QT application and click the "Factory Reset" function option to operate.

### Method 2

via the update command

```bash
update
# or
update factory / update reset
````


## Firmware local upgrade

Buildroot supports upgrading firmware from external storage devices. The following is the upgrade process description.For how to compile the Buildroot firmware, please refer to the **Compile Buildroot firmware** page of the corresponding board wiki.

### Make upgrade firmware

Follow the normal firmware compilation process to make the firmware for upgrade.

To upgrade firmware, it is not necessary to upgrade all partitions. You can modify the `package-file` file, comment out the partitions that do not need to be upgraded, or change to `RESERVED`, which can reduce the size of the upgraded firmware.

For example, modify the file `tools/linux/Linux_Pack_Firmware/rockdev/rk356x-package-file`, change the relative path of `rootfs` to `RESERVED`, so that the root filesystem will not be packaged, that is, the root filesystem partition will not be upgraded.

（1）modify the file `tools/linux/Linux_Pack_Firmware/rockdev/package-file`. 

For example, change the relative path of `rootfs` to `RESERVED`, so that the root filesystem will not be packaged, that is, the root filesystem partition will not be upgraded.

```bash
# name          relative path
#
#hwdef          hwdef
package-file    package-file
bootloader      image/miniloaderall.bin
parameter       image/parameter.txt
trust           image/trust.img
uboot           image/uboot.img
misc            image/misc.img
boot            image/boot.img
recovery        image/recovery.img
rootfs          RESERVED
oem             image/oem.img
userdata:grow   image/userdata.img
backup          RESERVED
```

（2）compile

```
./build.sh updateimg
```

Copy the prepared upgrade firmware to the U disk, TF card, or the `/userdata/` directory of the device, and rename it to `update.img`.

**Note:** If the upgrade firmware is placed in the `/userdata/` directory of the device, do not package `userdata.img`, and change `image/userdata.img` to `RESERVED`.

### Upgrade

#### method 1

Configuration via the qsetting QT application. Click the "Update" function option to operate.

#### Method 2

via the update command.

```bash
# U disk
update ota /udisk/update.img
# TF card
update ota /sdcard/update.img
# /userdata/
update ota /userdata/update.img
````

Wait for the upgrade to complete. After the upgrade is successful, the device will reboot into the system.


## Weston

We can customize the display by configuring Weston, some of the settings are explained below.

### Status Bar

Weston supports setting the background color and position of the status bar in the shell section of the weston.ini configuration file, as well as setting quick launch programs in the launcher section, such as:

```ini
# /etc/xdg/weston/weston.ini

[shell]
# The color format is ARGB8888
panel-color=0xff002244
# top|bottom|left|right|none
panel-position=bottom

[launcher]
icon=/usr/share/weston/terminal.png
path=/usr/bin/weston-terminal

[launcher]
# Icon path
icon=/usr/share/weston/icon_flower.png
# Quick start command
path=/usr/bin/qsetting
```

### Background

Weston supports setting background patterns and colors in the shell section of the weston.ini configuration file, such as:

```ini
# /etc/xdg/weston/weston.ini

[shell]
# Background pattern (wallpaper) absolute path
background-image=/usr/share/weston/background.png
# scale|scale-crop|tile
background-type=scale
# The color format is ARGB8888, which will take effect when the background pattern is not set
background-color=0xff002244
```

### Standby and lock screen

Weston's timeout standby time can be configured in the startup parameters or in the core section of weston.ini, such as:

```bash
# /etc/init.d/S50launcher
     start)
         ...
         # 0 is to prohibit standby, the unit is second
         weston --tty=2 -B=drm-backend.so --idle-time=0&
```

or:

```ini
# /etc/xdg/weston/weston.ini

[core]
# Set to enter the standby state after 5 seconds of inactivity
idle-time=5
```

### Display color format

The current default display format of Weston in Buildroot SDK is ARGB8888. For some low-performance platforms, it can be configured as RGB565 in the core section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[core]
# xrgb8888|rgb565|xrgb2101010
gbm-format=rgb565
```

You can also configure the display format of each screen separately in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
# The name of output can be viewed /sys/class/drm/card0-name
name=LVDS-1
# xrgb8888|rgb565|xrgb2101010
gbm-format=rgb565
```

### Screen orientation

Weston's screen display direction can be configured in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=LVDS-1
# normal|90|180|270|flipped|flipped-90|flipped-180|flipped-270
transform=180
```

If you need to dynamically configure the screen orientation, you can use a dynamic configuration file, such as:

```bash
echo "output:all:rotate90"> /tmp/.weston_drm.conf # All screens are rotated 90 degrees
echo "output:eDP-1:rotate180"> /tmp/.weston_drm.conf # eDP-1 rotate 180 degrees
```

### Resolution and scaling

The screen resolution and scaling of Weston can be configured in the output section of weston.ini, such as:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=HDMI-A-1
# Need to be an effective resolution supported by the screen
mode=1920x1080
# Must be an integer multiple
scale=2
```

If you need to dynamically configure resolution and scaling, you can use dynamic configuration files, such as:

```bash
echo "output:HDMI-A-1:mode=800x600"> /tmp/.weston_drm.conf # Modify the resolution of HDMI-A-1 to 800x600
```

You need to rely on RGA acceleration when scaling in this way.

### Freeze screen

When Weston was started, there was a black screen for a short period switching between the boot logo and the UI display. If you need to prevent a black screen, you can temporarily freeze the Weston screen content through the following dynamic configuration file methods:

```bash
# /etc/init.d/S50launcher
     start)
         ...
         export WESTON_FREEZE_DISPLAY=/tmp/.weston_freeze # Set the path of the special configuration file
         touch /tmp/.weston_freeze # Freeze display
         weston --tty=2 -B=drm-backend.so --idle-time=0&
         ...
         sleep 1 && rm /tmp/.weston_freeze& # Thaw in 1 second
```

### Multi-screen

The Weston of the Buildroot SDK supports functions such as multi-screen same-different display and hot-plugging. The distinction between different display screens is based on the drm name (obtained via /sys/class/drm/card0-name), and the relevant configuration is set through environment variables, such as:

```bash
# /etc/init.d/S50launcher

    start)
        ...
        export WESTON_DRM_PRIMARY=HDMI-A-1 # Specify the main display as HDMI-A-1
        export WESTON_DRM_MIRROR=1 # Use mirror mode (multiple screens at the same display), if you don’t set this environment variable, it will be a different display
        export WESTON_DRM_KEEP_RATIO=1 # The zoom maintains the aspect ratio in the mirror mode. If this variable is not set, the full screen is forced
        export WESTON_DRM_PREFER_EXTERNAL=1 # The built-in display is automatically turned off when the external display is connected
        export WESTON_DRM_PREFER_EXTERNAL_DUAL=1 # When the external display is connected, the first external display is the main display by default
        weston --tty=2 -B=drm-backend.so --idle-time=0&
```

When zooming the display content in the mirror mode, you need to rely on RGA acceleration.

At the same time, it is also supported to individually disable specific screens in the output section of weston.ini:

```ini
# /etc/xdg/weston/weston.ini

[output]
name=LVDS-1
mode=off
# off|current|preferred|<WIDTHxHEIGHT@RATE>
```

### Input device related

Weston service requires at least one input device by default. If there is no input device, special settings in the core section of weston.ini are required:

```poini
# /etc/xdg/weston/weston.ini

[core]
require-input=false
```


## Buildroot development

Buildroot is a framework for building embedded Linux systems on the Linux platform. The entire Buildroot is composed of a Makefile (*.mk) script and a Kconfig (Config.in) configuration file. You can compile a complete Linux system software (including boot, kernel, rootfs, and various libraries and applications in rootfs) that can be directly flashed to the machine through the buildroot configuration and menuconfig modification, just like compiling the Linux kernel. If you want to learn more about Buildroot development, you can refer to the official Buildroot *[The Buildroot user manual](https://buildroot.org/downloads/manual/manual.html)*.

Let's take the development of **ROC-RK3568-PC**'s **Buildroot** as an example to explain.

### Directory structure

The Buildroot SDK is located in the Firefly_Linux_SDK directory, and its directory structure is as follows:

```bash
buildroot/
├── arch                # Construction and configuration files of CPU architecture
├── board               # Documents related to specific boards
├── boot                # Construction and configuration files of Bootloaders
├── build
├── CHANGES             # Buildroot modification log
├── Config.in
├── Config.in.legacy
├── configs             # Buildroot configuration file of the specific board
├── COPYING
├── DEVELOPERS
├── dl                  # Downloaded programs, source code compressed packages, patches, etc.
├── docs                # Documentation
├── fs                  # Construction and configuration files of various filesystems
├── linux               # Construction and configuration files of Linux
├── Makefile
├── Makefile.legacy
├── output              # Compile output directory
├── package             # Construction and configuration files of all packages
├── README              # Simple instructions for Buildroot
├── support             # Scripts and configuration files that provide functional support for Bulidroot
├── system              # Construction and configuration files of making root filesystem
├── toolchain           # Construction and configuration files of cross-compilation toolchain
└── utils               # Utilities
```

### Configuration

Select the default profile:

```bash
# Enter the Firefly_Linux_SDK root directory
cd path/to/Firefly_Linux_SDK/
# Select configuration file
# `configs/rockchip_rk3568_defconfig`
source envsetup.sh rockchip_rk3568
```

After the execution is completed, a compilation output directory, `output/rockchip_rk3568` will be generated, and subsequent operations of `make` can be executed in this directory.

#### Configure package

Open the configuration interface:

```bash
make menuconfig
```

![](../../../img/buildroot/Buildroot_menuconfig.png)

We can add or cut some tools in the configuration interface to customize system functions as required. Take adding `qt53d` as an example:

Enter `/` to enter the search interface, enter the content you want to find `qt53d`, and press Enter to search:

![](../../../img/buildroot/Buildroot_serach_qt53d.png)

![](../../../img/buildroot/Buildroot_qt53d.png)

Select `1` to jump to the corresponding page, press the space to select the configuration:

![](../../../img/buildroot/Buildroot_select_qt53d.png)

After the configuration is completed, move to `Save` and press Enter to save to `.config`; move to `Exit` and press Enter to exit.

![](../../../img/buildroot/Buildroot_Save-Exit.png)

Save the configuration file:

```bash
make savedefconfig
```

Save the changes to the configuration file `configs/rockchip_rk3568_defconfig`.

#### Configure Busybox

Open the configuration interface and configure:

```bash
make busybox-menuconfig
```

![](../../../img/buildroot/Buildroot_busybox-menuconfig.png)

After the configuration is complete, move to `Exit` and press Enter to exit, select `Yes` in the pop-up window and save it to `.config`.

![](../../../img/buildroot/Buildroot_busybox-save.png)

Save the configuration file:

```bash
make busybox-update-config
```

Save the changes to the configuration file `board/rockchip/common/base/busybox.config`.

### Compile

After configuring Buildroot, run `make` directly to compile.

#### Compilation instructions

When you run `make` to compile, the following process will be executed:

1. Download the source code;
2. Configure, compile and install the cross-compilation toolchain;
3. Configure, compile and install the selected software package;
4. Generate the root filesystem according to the selected format;

More usages of `make` can be obtained through `make help`.

#### Compile the package

We can execute `make <package>` to compile a package separately. The compilation of the software package mainly includes the process of downloading, decompressing, patching, configuring, compiling, and installing. For details, please refer to `package/pkg-generic.mk`.

* Download

  Buildroot will automatically obtain the corresponding software packages from the Internet according to the configuration `package/<package>/<package>.mk`, including some third-party libraries, plug-ins, utilities, etc., and place them in the `dl/` directory.

* Unzip

  The package will be decompressed in the `output/rockchip_rk3568/build/<package>-<version>` directory.

* Patch

  Patches are placed in the `package/<packgae>/` directory, and Buildroot will apply the corresponding patches after decompressing the package. If you want to modify the source code, you can modify it by patching.

* Configure
* Compile
* Install

  After the compilation is completed, the required compilation files will be copied to the `output/rockchip_rk3568/target/` directory.

For a certain package, we can call a certain step in the package construction through `make <package>-<target>`, as follows:

```bash
Package-specific:
  <pkg>                  - Build and install <pkg> and all its dependencies
  <pkg>-source           - Only download the source files for <pkg>
  <pkg>-extract          - Extract <pkg> sources
  <pkg>-patch            - Apply patches to <pkg>
  <pkg>-depends          - Build <pkg>'s dependencies
  <pkg>-configure        - Build <pkg> up to the configure step
  <pkg>-build            - Build <pkg> up to the build step
  <pkg>-graph-depends    - Generate a graph of <pkg>'s dependencies
  <pkg>-dirclean         - Remove <pkg> build directory
  <pkg>-reconfigure      - Restart the build from the configure step
  <pkg>-rebuild          - Restart the build from the build step
```

### Output directory

After the compilation is complete, a subdirectory will be generated in the compilation output directory `output/rockchip_rk3568` as follows:

* `build/` contains all source files, including the required host tools and selected packages for Buildroot. This directory contains all package source code.
* `host/` Tools required for host-side compilation include cross-compilation tools.
* `images/` contains a compressed root filesystem image file.
* `staging/` This directory is similar to the directory structure of the filesystem. It contains all the header files and libraries generated by the compiler, as well as other development files, but they are not tailored and are too large to be used for the target filesystem.
* `target/` contains the complete root filesystem. Compared to `staging/`, it has no development files, no header files, and the binary files are `strip` processed.

### Cross-compilation tool

After Buildroot is compiled, it will generate a cross-compilation tool in the `output/rockchip_rk3568/host/` directory, which we can use to compile the target program.

* Cross-compilation tool directory

`output/rockchip_rk3568/host/bin/`

* Compile example hello.c

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
         printf("Hello World!\n");
         return 0;
}
```

* Compile

```bash
.../host/bin/arm-buildroot-linux-gnueabihf-gcc hello.c -o hello
```

* Run

Copy the executable program `hello` to the device, run `./hello`, you will see the printed message `Hello World!`.

### Rebuild

For specific instructions on rebuilding, you can check the document `buildroot/docs/manual/rebuilding-packages.txt`.

#### Rebuild the package

During the development process, if the source code of a certain package is modified, Buildroot will not recompile the package. It can be operated as follows:

* Method one

```bash
make <package>-rebuild
```

* Method two

```bash
# Delete the compiled output directory of the package
rm -rf output/rockchip_rk3568/build/<package>-<version>
# Compile
make <package>
```

#### Full Rebuild

Buildroot does not attempt to detect what parts of the system should be rebuilt when the system configuration is changed through `make menuconfig`, `make xconfig`, or one of the other configuration tools. In some cases, Buildroot should rebuild the entire system, in some cases, only a specific subset of packages. But detecting this in a completely reliable manner is very difficult, and therefore the Buildroot developers have decided to simply not attempt to do this.

##### When a full rebuild is necessary

* When the target architecture configuration is changed, a complete rebuild is needed;
* When the toolchain configuration is changed, a complete rebuild generally is needed;
* When an additional package is added to the configuration, a full rebuild is not necessarily needed;
* When a package is removed from the configuration, buildroot does not do anything special. it does not remove the files installed by this package from the target root filesystem or from the toolchain _sysroot_. a full rebuild is needed to get rid of this package;
* When the sub-options of a package are changed, the package is not automatically rebuilt;
* When a change to the root filesystem skeleton is made, a full rebuild is needed;

Generally speaking, when you're facing a build error and you're unsure of the potential consequences of the configuration changes you've made, do a full rebuild. Specific instructions can be found in the document `rebuilding-packages.txt`.

##### Full rebuild

* Method one

Directly delete the compilation output directory, and then re-configure and compile.

```bash
rm -rf output/
```

* Method two

Executing the following command will delete the compilation output and recompile.

```bash
make clean all
```

### Add local source package

During the development process, the built-in software package of Buildroot may not meet our needs sometimes, so we need to add a custom software package. Buildroot supports packages in a variety of formats, including generic-package, cmake-package, autotools-package, etc. We take generic-package as an example.

* Create a project directory

```bash
cd path/to/Firefly_Linux_SDK/
mkdir buildroot/package/rockchip/firefly_demo/
```

* Create Config.in

Add Config.in under `firefly_demo/`:

```Kconfig
config BR2_PACKAGE_FIREFLY_DEMO
        bool "Simple Firefly Demo"
```

* Create firefly_demo.mk

Add firefly_demo.mk under `firefly_demo/`:

```makefile
##################################################
###########
#
### firefly_demo
#
##################################################
###########
ifeq ($(BR2_PACKAGE_FIREFLY_DEMO), y)

        FIREFLY_DEMO_VERSION:=1.0.0
        FIREFLY_DEMO_SITE=$(TOPDIR)/../external/firefly_demo/src
        FIREFLY_DEMO_SITE_METHOD=local

define FIREFLY_DEMO_BUILD_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) CC=$(TARGET_CC) CXX=$(TARGET_CXX) -C $(@D)
endef

define FIREFLY_DEMO_CLEAN_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) clean
endef

define FIREFLY_DEMO_INSTALL_TARGET_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) install
endef

define FIREFLY_DEMO_UNINSTALL_TARGET_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) uninstall
endef

$(eval $(generic-package))
endif
```

* Create source code directory

The source code directory `external/firefly_demo/src` has been specified in the above Makefile.

```bash
cd path/to/Firefly_Linux_SDK/
mkdir external/firefly_demo/src
```

* Write source code firefly_demo.c

Add firefly_demo.c under `firefly_demo/src/`:

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
        printf("Hello World!\n");
        return 0;
}
```

* Write Makefile

Add Makefile under `firefly_demo/src/`:

```makefile
DEPS =
OBJ = firefly_demo.o
CFLAGS =
%.o: %.c $(DEPS)
        $(CC) -c -o $@ $< $(CFLAGS)

firefly_demo: $(OBJ)
        $(CXX) -o $@ $^ $(CFLAGS)

.PHONY: clean
clean:
        rm -f *.o *~ firefly_demo

.PHONY: install
install:
        cp -f firefly_demo $(TARGET_DIR)/usr/bin/

.PHONY: uninstall
uninstall:
        rm -f $(TARGET_DIR)/usr/bin/firefly_demo
```

* Modify the upper Config.in

Add a line at the end of `buildroot/package/rockchip/Config.in`:

```Kconfig
source "package/rockchip/firefly_demo/Config.in"
```

* Select package

Open the configuration menu `make menuconfig`, find firefly_demo and select the configuration.

* Compile

```bash
# Compile firefly_demo
make firefly_demo
# Package into the root filesystem
make
# If you modify the source code, recompile the package
make firefly_demo-rebuild
```

### rootfs-overlay

rootfs-overly is a pretty good feature, it can overwrite the specified file to a directory after the target filesystem is compiled. In this way, we can easily add or modify some files to the root filesystem.

Suppose we want to add the file `overlay-test` under the `/etc/` directory of the root filesystem, we can do as follows:

* Set rootfs-overlay root directory

Open the configuration menu `make menuconfig`, and add the root directory for coverage by setting the `BR2_ROOTFS_OVERLAY` option. For RK3568, the directory `board/rockchip/rk356x/fs-overlay/` has been added by default.

* Add files to the coverage directory

```bash
cd buildroot/board/rockchip/rk356x/fs-overlay/
mkdir etc/
touch etc/overlay-test
```

* Compile

```bash
make
```

* Download the root filesystem

Download the compiled root filesystem `output/rockchip_rk3568/images/rootfs.ext2` to the device. Start the device, you can see that the file `/etc/overlay-test` has been added.

You can also check the `target/` directory to verify whether the addition is successful:

```bash
ls buildroot/output/rockchip_rk3568/target/etc/overlay-test
```

### Qt cross-compilation environment

Firefly has extracted Buildroot's cross-compilation toolchain which supports plugins such as EGLFS, LinuxFB, and Wayland. You can directly use this toolchain to develop Qt applications on Buildroot without downloading and compiling SDK code.

```bash
Version: Qt-5.15.2
Host: x86-64 / Ubuntu 18.04
Devices: Firefly RK3568 RK3566  / Buildroot
```

- Download: [Buildroot Qt](https://drive.google.com/drive/folders/1iIyXQxMVCc81H9ImsxV5SB0XxiwRj_b1?usp=sharing)

- Deployment: See Qt5.1x.x_Release.md in toolchain package for details.

