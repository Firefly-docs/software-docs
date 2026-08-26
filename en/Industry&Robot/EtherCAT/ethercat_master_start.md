# EtherCAT Master Startup

## 4. Master Configuration and Startup

### 4.1 Selecting the EtherCAT Interface

Display network interfaces and MAC addresses:

```bash
ip -br link
ip link show eth0
```

Select an interface dedicated to EtherCAT and record its permanent MAC address. Do not use a MAC address that changes randomly.

If a system service manages the interface, make it unmanaged first. The configuration method differs by distribution. For example, with NetworkManager, use:

```bash
nmcli device set eth0 managed no
```

### 4.2 Loading the Generic IgH Driver

Generic driver example:

```bash
sudo modprobe ec_master main_devices=62:36:B8:01:5B:59
sudo modprobe ec_generic
```

Replace the example MAC address with the actual address of the dedicated EtherCAT interface. Module parameter names and configuration file paths may vary between distribution packages. Confirm them with:

```bash
modinfo ec_master
modinfo ec_generic
```

Some installation methods provide `/etc/ethercat.conf` or `/etc/sysconfig/ethercat`, where the master interface and driver can be configured:

```text
MASTER0_DEVICE="62:36:B8:01:5B:59"
DEVICE_MODULES="generic"
```

Follow the format required by the service script included with the installed package.

### 4.3 Verifying the Master

```bash
ls -l /dev/EtherCAT*
sudo ethercat master
sudo ethercat slaves
dmesg | grep -i ethercat
```

Normally, the master, link state, and slave list should be displayed. If no slaves are found:

1. Check the Ethernet cable direction, slave power, and link indicators.
2. Confirm that `main_devices` contains the correct MAC address.
3. When using `ec_generic`, confirm that the standard network driver works correctly and that normal network services are not sending or receiving IP traffic through the interface.
4. Check the actual device bound after the master is loaded.
5. Start testing with a single slave, then gradually restore the complete topology.

### 4.4 Stopping the Master

Stop the application and ensure that outputs have entered a safe state before unloading the drivers:

```bash
sudo modprobe -r ec_generic
sudo modprobe -r ec_master
```

When using a platform-specific driver, unload modules in the reverse order in which they were loaded.
