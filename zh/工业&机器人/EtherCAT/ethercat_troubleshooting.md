# EtherCAT 故障排查

## 7. 实时性优化与调试

### 7.6 常见故障

#### 找不到 `/dev/EtherCAT0`

检查：

```bash
lsmod | grep '^ec_'
dmesg | grep -i ethercat
modinfo ec_master
```

确认主站模块与当前内核版本完全匹配，并检查设备节点规则或主站服务是否正常执行。

#### 主站存在但扫描不到从站

- 检查 MAC 地址和主站绑定网口。
- 检查从站电源、IN/OUT 方向和网线。
- 使用 `ec_generic` 时确认标准网卡驱动正常、普通网络服务未使用该网口；使用原生或平台专用驱动时，确认标准网卡驱动没有占用该设备。
- 检查链路速率；EtherCAT 设备通常使用 100 Mbit/s 全双工。
- 从一个从站和一根短网线开始定位。

#### 从站停留在 PREOP

- 使用 `ethercat slaves -v` 查看 AL 状态和错误码。
- 检查 PDO 分配和同步管理器配置。
- 检查 SDO 初始化是否成功。
- 核对 Vendor ID、Product Code 和 Revision Number。

#### 从站停留在 SAFEOP

- 检查应用是否已开始周期收发。
- 检查工作计数是否完整。
- 检查看门狗、周期、DC 和 Sync0/Sync1 配置。
- 检查输出 PDO 是否按设备要求初始化。

#### 周期抖动较大

- 确认使用 PREEMPT_RT 和适合的实时网卡驱动。
- 检查实时线程是否被其他任务或 IRQ 抢占。
- 检查 CPU 调频、温控降频和深度休眠。
- 移除周期线程中的日志、内存分配和阻塞调用。
- 分别测试空载、单从站和完整拓扑。

#### `ethercat` 无法加载 `libethercat.so`

```bash
ldd "$(command -v ethercat)"
ldconfig -p | grep libethercat
```

将库安装到系统库目录并执行 `ldconfig`，或正确设置运行时库搜索路径。不要混用不同 IgH 版本的工具、库、头文件和内核模块。
