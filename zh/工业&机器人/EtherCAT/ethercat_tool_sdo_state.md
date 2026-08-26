# EtherCAT 工具：SDO 和状态操作

## 5. `ethercat` 工具使用

### 5.5 查看和读写 SDO

列出对象字典：

```bash
ethercat sdos -p 0
```

读取对象 `0x1018:01`：

```bash
ethercat upload -p 0 0x1018 1
```

指定数据类型读取：

```bash
ethercat upload -p 0 --type uint32 0x1018 1
```

写入 SDO 前必须核对设备手册、数据类型和单位。例如：

```bash
ethercat download -p 0 --type uint16 0x2000 0 1
```

错误的 SDO 写入可能改变电机参数或持久化配置。生产设备上应先备份参数，并避免在电机运行时修改关键对象。

### 5.6 切换状态和重新扫描

```bash
ethercat states PREOP
ethercat states SAFEOP
ethercat states OP
ethercat rescan
```

状态切换失败时，先查看从站错误和主站日志，不要连续强制进入 `OP`。

### 5.7 导出从站信息

```bash
ethercat xml -p 0 > slave.xml
ethercat sii_read -p 0 > slave-sii.bin
```

不同 IgH 版本提供的命令可能不同。写入 SII/EEPROM 风险较高，除非明确掌握从站数据格式，否则只进行读取和备份。
