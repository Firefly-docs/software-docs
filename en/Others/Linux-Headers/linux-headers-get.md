# linux-headers

linux-headers include header files, can make the device able to compile drivers.

## How to get

### Download from Firefly

Kernel 5.10 or Kernel 4.19 Linux-Headers: For the package download of each board, please go to the official Firefly *[Download](http://en.t-firefly.com/doc/download/61.html)* page to download. After selecting the board, click the **Download** page, it is generally in the **Resources** section, and the name is **linux-headers**.

Kernel 6.1 Linux-Headers: By default, the SDK for kernel 6.1 will compile linux-headers into extboot.img. You can find linux-headers-6.1-arm64_arm64.deb in the /boot/ directory of the device.

### Build from SDK

The version of headers and image downloaded above may mismatch your firmware, and they are not helpful with customization needs. So build them from SDK is recommended.

Prepare environment, get SDK and comfigure for compile, please check the specific deivce's wiki.

Build under SDK root directory:
```bash
./build.sh kerneldeb
```
Output files are in SDK root directory:
```
linux-headers-x.xx.xxx_x.xx.xxx-xxx_arm64.deb
```
