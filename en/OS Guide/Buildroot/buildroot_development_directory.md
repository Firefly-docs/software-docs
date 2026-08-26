# Buildroot Directory Structure

The Buildroot SDK is located in the Firefly_Linux_SDK directory, and its directory structure is as follows:

```bash
buildroot/
├── arch                # Construction and configuration files of CPU architecture
├── board               # Documents related to specific boards
├── boot                # Construction and configuration files of Bootloaders
├── build
├── CHANGES             # Buildroot modification log
├── Config.in
├── Config.in.legacy
├── configs             # Buildroot configuration file of the specific board
├── COPYING
├── DEVELOPERS
├── dl                  # Downloaded programs, source code compressed packages, patches, etc.
├── docs                # Documentation
├── fs                  # Construction and configuration files of various filesystems
├── linux               # Construction and configuration files of Linux
├── Makefile
├── Makefile.legacy
├── output              # Compile output directory
├── package             # Construction and configuration files of all packages
├── README              # Simple instructions for Buildroot
├── support             # Scripts and configuration files that provide functional support for Bulidroot
├── system              # Construction and configuration files of making root filesystem
├── toolchain           # Construction and configuration files of cross-compilation toolchain
└── utils               # Utilities
```
