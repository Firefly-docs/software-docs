# EtherCAT Tool

## 5. Using the `ethercat` Tool

### 5.1 Displaying Help

```bash
ethercat --help
ethercat slaves --help
ethercat pdos --help
```

Most commands support the following selection options:

- `-m <master>`: Select a master by number.
- `-p <position>`: Select a slave by physical position.
- `-a <alias>`: Select a slave by alias address.

Refer to `ethercat <command> --help` on the target system for the exact options.

### 5.2 Displaying Masters and Slaves

```bash
ethercat master
ethercat slaves
```

Typical slave output contains the position, alias, state, and device name:

```text
0  0:0  PREOP  +  Servo Drive
```

Display detailed information:

```bash
ethercat slaves -v
ethercat config
```

### 5.3 Setting an Alias Address

Set the alias of the slave at position `0` to `1`:

```bash
ethercat alias -p 0 1
```

The alias is written to the slave EEPROM. Confirm the target position before running the command to avoid modifying the wrong device.

### 5.4 Displaying PDOs

```bash
ethercat pdos -p 0
ethercat cstruct -p 0
```

`ethercat pdos` displays the slave's current PDO assignment. `ethercat cstruct` generates PDO entries and Sync Manager structures that can be used as a reference by a C application.

The automatically generated structures reflect only the current device configuration. Check the indexes, subindexes, bit lengths, directions, and Sync Manager numbers against the slave manual.
