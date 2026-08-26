# AMP Development Recommendations

## 13. Development recommendations

- Copy an existing AMP board configuration from the SDK instead of starting from empty DTS and ITS files.
- Run the supplied demo with the default memory and RPMsg configuration before making product-specific changes.
- Maintain one address allocation table covering Linux, every Remote Core, and all shared-memory regions.
- Treat a peripheral's clock, reset, pinctrl, power domain, and IRQ as one resource bundle when transferring ownership.
- Use RPMsg for control and small-to-medium messages. Use a dedicated shared buffer for high-bandwidth data.
- Remote firmware should implement its own watchdog, timeout handling, error states, and safe degradation behavior.
- Before production, perform repeated cold boots, warm reboots, high-load runs, long-duration RPMsg tests, and peripheral interrupt stress tests.

## 14. Terminology

| Term | Description |
| --- | --- |
| AMP | Asymmetric Multi-Processing |
| SMP | Symmetric Multi-Processing |
| AP | Application Processor, normally a Cortex-A core |
| MCU | Microcontroller Unit, such as a Cortex-M or RISC-V core |
| RTOS | Real-Time Operating System |
| RTT | RT-Thread in this document |
| HAL | Hardware Abstraction Layer |
| FIT/ITS | U-Boot Flattened Image Tree and its source description file |
| RPMsg | Remote Processor Messaging, an inter-core messaging protocol |
| RPMsg-Lite | A lightweight RPMsg implementation for RTOS and bare-metal systems |
| vring | The ring queue used by VirtIO |
| Master Core | The core that manages shared resources and communication memory |
| Remote Core | A core loaded or managed by the Master Core |
