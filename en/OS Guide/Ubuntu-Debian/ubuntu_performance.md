# Ubuntu/Debian Performance Mode

To keep each module running at its highest frequency as much as possible, switch to the root user and run:

```bash
sudo su
echo performance | tee $(find /sys/ -name '*governor')
```

Some nodes may report errors such as `Permission denied` or `Invalid argument`. This means those governor nodes do not support user-space writes or do not support the `performance` value. The errors do not affect other nodes that were updated and can be ignored.

Performance mode switches supported CPU, devfreq, and similar modules to the `performance` governor, but if the system temperature becomes too high, the thermal mechanism will still force frequency throttling to protect the hardware.
