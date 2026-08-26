# Build the Rockchip EtherCAT Driver

## 8. Rockchip Platform Support

### 8.5 Building

Build directly on the target machine:

```bash
./bootstrap

# Use --prefix to specify the installation directory for user-space programs
./configure \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-8139too=no \
  --enable-stmmac=yes \
  --enable-generic=no \
  --enable-wildcards=yes

```

Build and install the files:

```bash
make -j8 all modules
make modules_install install
```

Use `modprobe` and module dependency management:

```bash
sudo depmod -a

sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_stmmac
```

Replace `main_devices` with the actual MAC address of the EtherCAT GMAC:

```bash
ip link
cat /sys/class/net/eth0/address
```

After loading the modules, check:

```bash
dmesg | tail -n 100
ls -l /dev/EtherCAT0
ethercat master
ethercat slaves
```

If `ecdev` for the platform `stmmac` driver is `0x0` in the log, the most common cause is an incorrect MAC address in `main_devices`. Also check whether the target GMAC has been claimed by the standard driver and whether the correct interface is selected in the device tree.
