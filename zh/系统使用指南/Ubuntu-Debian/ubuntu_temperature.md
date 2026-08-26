# Ubuntu/Debian SoC 各模块温度

SoC 各模块温度可通过 thermal 节点查看。先查看各 thermal zone 的类型：

```bash
cat /sys/class/thermal/thermal_zone*/type
```

示例输出：

```text
soc-thermal
bigcore0-thermal
bigcore1-thermal
littlecore-thermal
center-thermal
gpu-thermal
npu-thermal
```

再查看对应 thermal zone 的温度：

```bash
cat /sys/class/thermal/thermal_zone*/temp
```

示例输出：

```text
38846
38846
38846
38846
38846
38846
38846
```

温度单位为毫摄氏度，数值除以 1000 即为摄氏度，例如 `38846` 表示约 `38.846°C`。不同 SoC 和固件版本的 thermal zone 数量、名称可能不同，请以实际节点输出为准。
