# Build and Install the IgH Master

## 3. Building and Installing the IgH Master

This chapter describes the generic IgH build procedure. When using a customized package from the Rockchip SDK, refer first to [Chapter 8](ethercat_rockchip_support.md), because its master and `stmmac` driver include platform-specific adaptations.

### 3.1 Obtaining the Source Code

```bash
git clone https://gitlab.com/etherlab.org/ethercat.git
cd ethercat
```

For product development, pin the source to a verified tag or commit instead of depending directly on the continuously changing `master` branch.

### 3.2 Configuring Build Options

First generate `configure`:

```bash
./bootstrap
```

Native build example:

```bash
./configure \
  --prefix=/opt/etherlab \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-generic=yes
```

Cross-compilation example:

```bash
export ARCH=arm64
export CROSS_COMPILE=aarch64-none-linux-gnu-

./configure \
  --host=aarch64-none-linux-gnu \
  --prefix=/opt/etherlab \
  --with-linux-dir=/path/to/kernel \
  --enable-generic=yes
```

Common options:

| Option | Description |
| --- | --- |
| `--with-linux-dir=<path>` | Specifies the configured target kernel source directory |
| `--enable-generic=yes` | Builds the generic network driver `ec_generic` |
| `--enable-<driver>=yes` | Builds the specified native network driver supported by IgH |
| `--enable-wildcards=yes` | Allows wildcard matching in master device configuration; a fixed interface is still recommended for products |
| `--prefix=<path>` | Specifies the installation path for user-space libraries and tools |

Available driver options vary by IgH version. Check the output from the current source tree:

```bash
./configure --help | less
```

### 3.3 Building and Installing

```bash
make -j$(nproc) all modules
sudo make modules_install install
sudo depmod -a
```

For cross-compilation, you can first install the files into a staging directory:

```bash
make -j$(nproc) all modules
make DESTDIR=$PWD/output modules_install install
```

Then deploy the kernel modules, shared libraries, headers, tools, and service files under `output` to the corresponding directories on the target system.

### 3.4 Verifying the Installation

```bash
find /lib/modules/$(uname -r) -name 'ec_*.ko*'
command -v ethercat
ldconfig -p | grep libethercat
```

If `ethercat` runs but reports that it cannot open the master device, continue checking the kernel modules and `/dev/EtherCAT0` instead of checking only the user-space tool.
