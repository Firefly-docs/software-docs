# RTLinux Preface and Overview

## Preface

This document describes the basic usage of Linux kernel real-time patches and helps developers quickly understand and use a real-time system.

---

## Overview

Select the corresponding real-time system kernel patch based on the current kernel version. You can check the kernel version in `kernel/Makefile`:

```shell
# SPDX-License-Identifier: GPL-2.0
VERSION = 6
PATCHLEVEL = 1
SUBLEVEL = 118
...
```
