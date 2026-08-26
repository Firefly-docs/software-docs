# Major-Version or Partition-Layout Migration

Do not reuse a routine same-version OTA flow for a change in the Android major version, Linux distribution, partition layout, or data-encryption scheme. Before such a migration, verify at least the following:

- Compatibility among the bootloader, kernel, Recovery, and user-space components.
- Partition names, offsets, sizes, and file systems in both the source and target firmware.
- Changes to Android Verified Boot, signing keys, rollback indexes, and data encryption.
- Whether userdata can be migrated in place. Back up the data and perform a full flash if this cannot be guaranteed.

Design a dedicated migration and rollback procedure, and test it from every supported source version. If the target SDK does not explicitly support the migration through OTA, fully flash the target firmware with RKDevTool.
