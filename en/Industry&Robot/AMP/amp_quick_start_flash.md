# AMP Quick Start: Partition and Flash

## 5. Quick start

### 5.5 Configure the AMP partition

When booting from eMMC or SPI flash, the partition table needs an `amp` partition large enough for `amp.img`. First display the current partition table:

```bash
./build.sh list-parts
```

For example, insert a 2 MiB AMP partition at position 4:

```bash
./build.sh insert-part:4:amp:2M
./build.sh list-parts
```

The partition must be larger than the actual `amp.img` and should include room for future growth. After changing the partition table, update `parameter.txt` as required or regenerate the complete firmware image.

### 5.6 Flash and verify startup

Use the firmware upgrade procedure for the target Firefly board:

- If the partition table changed, flash the newly generated complete firmware image.
- If the partition table did not change, update `uboot.img`, `boot.img`, and `amp.img` individually according to the board's upgrade guide.
- If the solution uses a special BL31 or Loader, update the corresponding boot firmware as well.

During boot, first confirm that U-Boot successfully released the remote core. A typical RK3562 CPU3 message is:

```text
AMP: Brought up cpu[3] with state 0x10, entry 0x01800000 ...OK
```

Typical bare-metal remote-core output:

```text
Hello RK3562 Bare-metal using RK_HAL!
CPI_ID(3)
CPU(3) Initial OK!
```

An RT-Thread remote core should print its RT-Thread version and then enter the shell or main application.
