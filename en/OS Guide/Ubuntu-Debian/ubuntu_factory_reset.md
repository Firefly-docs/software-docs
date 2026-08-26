# Ubuntu/Debian Factory Reset

Firefly Ubuntu/Debian firmware that includes the `recovery` tool supports restoring factory settings. This means restoring the device to the initial state after the last firmware upgrade. Back up important data before running the command.

Show recovery usage:

```bash
recovery
```

The recovery tool includes the following reset option:

```text
factory | reset:
        reset to factory
```

Restore factory settings:

```bash
sudo recovery reset
```
