# RTLinux

## Preface

This document describes the basic usage of Linux kernel real-time patches and helps developers quickly understand and use real-time systems.

---

## Introduction

**RT-Linux** does not refer to a single operating system. It is **a collection of solutions that give the Linux kernel real-time capabilities**. These solutions share the goal of preserving the general Linux ecosystem, including drivers, libraries, and development tools, while adding timing determinism to task execution so that high-priority tasks respond within defined time limits. The main approaches are **PREEMPT_RT**, which modifies the Linux kernel itself to make it preemptible, and **Xenomai**, which adds a higher-priority real-time core outside Linux. They make different trade-offs in real-time performance, ecosystem compatibility, and development complexity.
