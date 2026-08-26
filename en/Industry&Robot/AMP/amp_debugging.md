# AMP Debugging

## 12. Debugging and troubleshooting

### 12.1 Recommended debug sequence

1. Boot only RTOS/HAL and verify the entry address, linker address, and debug UART.
2. Add Linux without RPMsg and verify that both sides start reliably.
3. Check `/proc/iomem`, DTS reserved memory, and the linked address ranges of every ELF file.
4. Enable Mailbox/SoftIRQ and RPMsg, and run the SDK demo first.
5. Transfer actual peripherals and application code one resource type at a time.

Useful Linux-side checks:

```bash
cat /proc/iomem
cat /proc/interrupts
dmesg | grep -Ei 'amp|rpmsg|virtio|mailbox|reserved|failed|error'
ls /sys/firmware/devicetree/base/reserved-memory/
```

Inspect the entry point and segments of an RTOS/HAL ELF file:

```bash
<cross-readelf> -h <firmware>.elf
<cross-readelf> -l <firmware>.elf
<cross-nm> -n <firmware>.elf | head
```

### 12.2 Common issues

| Symptom | Common cause | Resolution |
| --- | --- | --- |
| U-Boot does not print `Brought up cpu` | U-Boot was not built with `rk-amp.config`, the `amp` partition is missing, or FIT validation failed | Check the U-Boot configuration, partition table, `amp.img`, and `loadables` |
| Remote core faults immediately after startup | ITS `load` does not match the linked address, or the firmware exceeds its assigned region | Compare the ITS, map file, ELF program headers, and DTS |
| Linux crashes intermittently or data is corrupted | Remote-core memory is not reserved in Linux or overlaps CMA/another reserved region | Check `reserved-memory`, `/proc/iomem`, and the actual DDR capacity |
| RTOS/HAL has no serial output | UART is owned by Linux, the IOMUX group is wrong, the clock is not retained, or the baud rate differs | Disable the Linux UART node and check AMP clocks/pinctrl and board IOMUX |
| A peripheral works in polling mode but not with interrupts | GIC/NVIC/IPIC routing is wrong, or the peripheral interrupt source is disabled | Check the interrupt ID, target CPU, ISR, and peripheral registers in sequence |
| RPMsg only reports `host is online` | The remote is not running, shared addresses differ, or Name Service was not announced | Check remote logs, vring addresses, link ID, and endpoint name |
| RPMsg occasionally loses messages or hangs | Cache attributes differ, shared memory overlaps another region, or memory barriers are missing | Restore the SDK's default uncached configuration and check MMU/MPU mappings |
| `/dev/ttyRPMSG*` is missing | `CONFIG_RPMSG_TTY` is disabled or the remote did not announce `rpmsg-tty` | Check the Kernel configuration and remote Name Service |
| A newly assigned peripheral fails in both Linux and RTOS | Both sides still manage the peripheral, pinctrl, reset, or clock | Establish a single owner, disable the Linux DTS device, and retain required resources through the AMP node |

### 12.3 JTAG debugging

- Cortex-A RTOS/HAL firmware can be debugged with OpenOCD and JTAG by loading the ELF symbols for the corresponding CPU.
- A Cortex-M MCU can be debugged with J-Link/Ozone using connection scripts and address mappings designed for the target SoC.
- The ELF loaded into the debugger must come from the same build as the `amp.img` currently running on the board.
