# AMP 快速上手：分区与烧录

## 5. 快速上手

### 5.5 配置 AMP 分区

使用 eMMC 或 SPI Flash 启动时，分区表中需要有容纳 `amp.img` 的 `amp` 分区。先查看当前分区：

```bash
./build.sh list-parts
```

例如，在第 4 个位置插入一个 2 MiB 的 AMP 分区：

```bash
./build.sh insert-part:4:amp:2M
./build.sh list-parts
```

分区大小必须大于实际 `amp.img`，并考虑后续功能增长。修改分区表后通常需要同步更新 `parameter.txt` 或重新生成完整固件。

### 5.6 烧录与启动验证

按 Firefly 板卡对应的固件升级方法烧录：

- 更改了分区表时，建议烧录新生成的完整固件。
- 分区表未变更时，可按开发板升级文档单独更新 `uboot.img`、`boot.img` 和 `amp.img`。
- 使用特殊 BL31/Loader 的方案，还必须同步更新相应引导固件。

启动时先检查 U-Boot 是否成功释放从核。以 RK3562 CPU3 为例：

```text
AMP: Brought up cpu[3] with state 0x10, entry 0x01800000 ...OK
```

Bare-metal 从核的典型输出：

```text
Hello RK3562 Bare-metal using RK_HAL!
CPI_ID(3)
CPU(3) Initial OK!
```

RT-Thread 从核应输出 RT-Thread 版本信息并进入 shell 或主应用。
