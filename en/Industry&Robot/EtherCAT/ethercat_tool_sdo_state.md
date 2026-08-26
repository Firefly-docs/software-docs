# EtherCAT Tool: SDO and State Operations

## 5. Using the `ethercat` Tool

### 5.5 Displaying, Reading, and Writing SDOs

List the object dictionary:

```bash
ethercat sdos -p 0
```

Read object `0x1018:01`:

```bash
ethercat upload -p 0 0x1018 1
```

Read with a specified data type:

```bash
ethercat upload -p 0 --type uint32 0x1018 1
```

Before writing an SDO, verify the device manual, data type, and units. For example:

```bash
ethercat download -p 0 --type uint16 0x2000 0 1
```

An incorrect SDO write may change motor parameters or persistent configuration. Back up parameters before working on production equipment, and do not modify critical objects while a motor is running.

### 5.6 Changing States and Rescanning

```bash
ethercat states PREOP
ethercat states SAFEOP
ethercat states OP
ethercat rescan
```

If a state transition fails, check slave errors and master logs first. Do not repeatedly force the slaves into `OP`.

### 5.7 Exporting Slave Information

```bash
ethercat xml -p 0 > slave.xml
ethercat sii_read -p 0 > slave-sii.bin
```

Commands may differ between IgH versions. Writing SII/EEPROM data is high risk. Unless you fully understand the slave data format, only read and back up the data.
