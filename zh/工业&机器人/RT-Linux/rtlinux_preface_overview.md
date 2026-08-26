# RTLinux 前言与概要

## 前言

本文介绍 Linux 内核实时性补丁的基本使用方法，帮助开发者快速了解并使用实时系统。

---

## 概要

根据当前内核版本选择对应的实时系统内核补丁。可通过查看 `kernel/Makefile` 确认内核版本：

```shell
# SPDX-License-Identifier: GPL-2.0
VERSION = 6
PATCHLEVEL = 1
SUBLEVEL = 118
...
```
