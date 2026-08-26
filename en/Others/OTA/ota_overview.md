# Rockchip OTA Upgrade

OTA, or Over-the-Air upgrade, updates a device through a network or a local package without reflashing it with RKDevTool every time.

This guide focuses on routine upgrades within the same Android or Linux system version, such as bug fixes, application updates, kernel replacement, or root file-system updates. Notes for major-version migration are provided at the end.

Implementations differ among SoCs, system versions, and SDK branches. Always follow the partition table, configuration, and upgrade tools shipped with the target product SDK.

## Solution Overview

| System | Solution | Installation | Main characteristic |
| --- | --- | --- | --- |
| Android | Recovery OTA | Reboot into Recovery and write partitions | Simple layout and lower storage use |
| Android | A/B OTA | Write the inactive slot while Android runs | Seamless installation and rollback |
| Linux | Recovery OTA | updateEngine selects Recovery installation | Supports remote or local update.img |
| Linux | A/B OTA | Write the inactive slot | Higher reliability with more storage use |

Always verify the complete target firmware with a full flash before building an OTA package.
