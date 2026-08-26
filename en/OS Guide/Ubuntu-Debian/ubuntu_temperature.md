# Ubuntu/Debian SoC Module Temperatures

SoC module temperatures can be read from thermal nodes. First, show the type of each thermal zone:

```bash
cat /sys/class/thermal/thermal_zone*/type
```

Example output:

```text
soc-thermal
bigcore0-thermal
bigcore1-thermal
littlecore-thermal
center-thermal
gpu-thermal
npu-thermal
```

Then show the temperature of the corresponding thermal zones:

```bash
cat /sys/class/thermal/thermal_zone*/temp
```

Example output:

```text
38846
38846
38846
38846
38846
38846
38846
```

The unit is millidegrees Celsius. Divide the value by 1000 to get degrees Celsius. For example, `38846` means about `38.846°C`. The number and names of thermal zones may vary between SoCs and firmware versions. Use the actual node output as the reference.
