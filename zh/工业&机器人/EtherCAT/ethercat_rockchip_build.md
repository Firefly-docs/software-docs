# 构建 Rockchip EtherCAT 驱动

## 8. Rockchip 平台支持

### 8.5 编译

直接到目标机器编译

```bash
./bootstrap

# 可通过--prefix=指定用户态程序的安装目录
./configure \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-8139too=no \
  --enable-stmmac=yes \
  --enable-generic=no \
  --enable-wildcards=yes

```

编译并暂存安装文件：

```bash
make -j8 all modules
make modules_install install
```

使用 `modprobe` 和模块依赖管理：

```bash
sudo depmod -a

sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_stmmac
```

将 `main_devices` 替换为 EtherCAT GMAC 的真实 MAC 地址：

```bash
ip link
cat /sys/class/net/eth0/address
```

加载后检查：

```bash
dmesg | tail -n 100
ls -l /dev/EtherCAT0
ethercat master
ethercat slaves
```

如果日志中平台 `stmmac` 驱动的 `ecdev` 为 `0x0`，最常见原因是 `main_devices` 的 MAC 地址错误。还应检查目标 GMAC 是否已被标准驱动占用，以及设备树是否选择了正确网口。
