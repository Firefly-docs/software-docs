# Linux OTA

### Introduction and Flow

A Firefly single-system Linux OTA commonly uses updateEngine and Recovery:

1. The Normal system downloads or reads update.img.
2. updateEngine writes the upgrade flag to misc.
3. The device reboots into Recovery.
4. Recovery parses update.img and writes the selected partitions.
5. The device reboots into the Normal system.

The package is a Rockchip update.img. Its contents are selected by:

```text
tools/linux/Linux_Pack_Firmware/rockdev/package-file
```

Unneeded partitions can be marked RESERVED or removed to reduce package size.

A single-system Recovery layout must contain misc and recovery. userdata is commonly used to hold the downloaded package. If update.img is stored on userdata, do not move or overwrite userdata in the same update.

### Build the Package

Fully flash and verify the target firmware first. Then edit package-file and run the SDK packaging command, commonly:

```bash
./build.sh updateimg
```

### Install updateEngine

On Firefly Ubuntu 20.04 or 22.04:

```bash
sudo apt update
sudo apt install updateengine
```

Build from source on another distribution:

```bash
sudo apt install libdrm-dev libssl-dev libbz2-dev libcurl4-openssl-dev git
git clone https://gitlab.com/firefly-linux/external/recovery.git
cd recovery
make -j8
```

### Remote and Local Upgrade

Start an HTTP server in the directory containing update.img:

```bash
python3 -m http.server 8000
```

Run on the device:

```bash
updateEngine --misc=update --image_url=http://192.168.1.100:8000/update.img --savepath=/userdata/update.img --partition=0xFFFC00 --reboot
```

For a local package:

```bash
updateEngine --misc=update --image_url=/userdata/update.img --partition=0xFFFC00 --reboot
```

The documented example uses 0xFFFC00, while some SDKs use 0x3FFC00 by default. Partition mask definitions can change, so check the updateEngine help or source in the current SDK.

After installation, verify the system version, kernel, U-Boot, rootfs, hardware functions, preserved userdata, and Recovery logs.
