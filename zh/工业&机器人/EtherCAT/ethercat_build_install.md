# 构建和安装 IgH 主站

## 3. IgH 主站构建与安装

本章介绍通用 IgH 构建流程。使用 Rockchip SDK 定制包时，优先参考[第 8 章](ethercat_rockchip_support.md)，因为其主站和 `stmmac` 驱动包含平台适配。

### 3.1 获取源码

```bash
git clone https://gitlab.com/etherlab.org/ethercat.git
cd ethercat
```

产品开发应固定到经过验证的 tag 或 commit，不建议直接依赖持续变化的 `master` 分支。

### 3.2 配置构建选项

先生成 `configure`：

```bash
./bootstrap
```

本机编译示例：

```bash
./configure \
  --prefix=/opt/etherlab \
  --with-linux-dir=/lib/modules/$(uname -r)/build \
  --enable-generic=yes
```

交叉编译示例：

```bash
export ARCH=arm64
export CROSS_COMPILE=aarch64-none-linux-gnu-

./configure \
  --host=aarch64-none-linux-gnu \
  --prefix=/opt/etherlab \
  --with-linux-dir=/path/to/kernel \
  --enable-generic=yes
```

常用选项：

| 选项 | 说明 |
| --- | --- |
| `--with-linux-dir=<path>` | 指定已经配置过的目标内核源码目录 |
| `--enable-generic=yes` | 构建通用网卡驱动 `ec_generic` |
| `--enable-<driver>=yes` | 构建 IgH 支持的指定原生网卡驱动 |
| `--enable-wildcards=yes` | 允许主站设备配置使用通配匹配；产品中仍建议固定网口 |
| `--prefix=<path>` | 指定用户态库和工具的安装路径 |

可用驱动选项随 IgH 版本变化，应以当前源码输出为准：

```bash
./configure --help | less
```

### 3.3 编译和安装

```bash
make -j$(nproc) all modules
sudo make modules_install install
sudo depmod -a
```

如果是交叉编译，可先安装到暂存目录：

```bash
make -j$(nproc) all modules
make DESTDIR=$PWD/output modules_install install
```

然后将 `output` 中的内核模块、共享库、头文件、工具和服务文件部署到目标系统的对应目录。

### 3.4 检查安装结果

```bash
find /lib/modules/$(uname -r) -name 'ec_*.ko*'
command -v ethercat
ldconfig -p | grep libethercat
```

如果 `ethercat` 能运行但提示无法打开主站设备，应继续检查内核模块和 `/dev/EtherCAT0`，而不是只检查用户态工具。
