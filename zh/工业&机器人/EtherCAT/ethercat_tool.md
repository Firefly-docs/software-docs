# EtherCAT 工具

## 5. `ethercat` 工具使用

### 5.1 查看帮助

```bash
ethercat --help
ethercat slaves --help
ethercat pdos --help
```

大多数命令支持以下选择方式：

- `-m <master>`：选择主站编号。
- `-p <position>`：按物理位置选择从站。
- `-a <alias>`：按别名地址选择从站。

具体参数以目标系统上的 `ethercat <command> --help` 为准。

### 5.2 查看主站和从站

```bash
ethercat master
ethercat slaves
```

典型从站输出包含位置、别名、状态和设备名称：

```text
0  0:0  PREOP  +  Servo Drive
```

查看详细信息：

```bash
ethercat slaves -v
ethercat config
```

### 5.3 设置别名地址

将位置 `0` 的从站别名设置为 `1`：

```bash
ethercat alias -p 0 1
```

别名会写入从站 EEPROM。执行前应确认目标位置，避免修改错误设备。

### 5.4 查看 PDO

```bash
ethercat pdos -p 0
ethercat cstruct -p 0
```

`ethercat pdos` 显示从站当前 PDO 分配，`ethercat cstruct` 生成可供 C 程序参考的 PDO 条目和同步管理器结构。

自动生成的结构只反映当前设备配置。仍应结合从站手册检查索引、子索引、位宽、方向和同步管理器编号。
