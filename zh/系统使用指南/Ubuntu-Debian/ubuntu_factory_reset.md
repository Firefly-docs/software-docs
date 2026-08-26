# Ubuntu/Debian 恢复出厂设置

内置 `recovery` 工具的 Firefly Ubuntu/Debian 固件支持恢复出厂设置。这里的出厂设置表示恢复为设备最后一次升级固件之后的初始状态。执行前请备份重要数据。

查看 recovery 用法：

```bash
recovery
```

恢复工具包含以下 reset 选项：

```text
factory | reset:
        reset to factory
```

执行恢复出厂设置：

```bash
sudo recovery reset
```
