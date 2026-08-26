# EtherCAT Preparation

## 2. Preparation

### 2.1 Hardware Requirements

At least the following equipment is required:

1. A master device running Linux with a compatible, dedicated Ethernet interface.
2. One or more EtherCAT slaves.
3. Ethernet cables, power supplies, power cables, and encoder cables that meet the device requirements.
4. The slave manuals and ESI XML files.
5. Emergency stops, limit switches, and mechanical safeguards required for servo system commissioning.

EtherCAT slaves should be connected in series according to the port direction. Some devices clearly distinguish between `IN` and `OUT` ports; reversing them may prevent the master from scanning the bus successfully.

### 2.2 Software Requirements

The following software environment is recommended:

- Linux kernel source and a cross-compilation toolchain matching the target board.
- The IgH EtherCAT Master source code or a prebuilt package provided by the platform SDK.
- `make`, Autoconf, Automake, Libtool, Pkg-config, and a C compiler.
- A PREEMPT_RT kernel for applications with strict real-time requirements.
- Tools such as `phc2sys` and `ptp4l` from `linuxptp` when using DC, TSN, or hardware timestamps.

### 2.3 Safety Information

Normal EtherCAT communication does not mean that a motor can be operated safely. Observe the following precautions when commissioning servo drives:

- Disconnect power output or raise the mechanical load before the first test.
- Verify the emergency stop, limit switch, and drive alarm logic before enabling the motor.
- Check the control word, operating mode, target position, target velocity, and unit conversions.
- When the application exits, the link is disconnected, or the master enters an error state, write a safe control word and stop the outputs.
- Do not apply the target velocity, position, or torque values from an example directly to real equipment.
